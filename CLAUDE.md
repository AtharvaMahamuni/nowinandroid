# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Now in Android** is a fully functional Android app built entirely with Kotlin and Jetpack Compose. It follows Android's official architecture guidance and serves as a reference implementation for Android development best practices. The app displays content from the "Now in Android" series and allows users to browse and follow topics of interest.

## Build System & Development Commands

### Building the App

The project uses Gradle with convention plugins located in the `build-logic` directory. Use `./gradlew` (or `gradlew.bat` on Windows).

**Build variants:**
- Use `demoDebug` for normal development (uses static local data)
- Use `demoRelease` for UI performance testing
- `prod` variants require a backend server (not publicly available)
- `benchmark` variant is for startup performance testing and baseline profile generation

**Common commands:**
```bash
# Build the demo debug variant
./gradlew assembleDemoDebug

# Build the demo release variant
./gradlew assembleDemoRelease

# Install and run on device
./gradlew installDemoDebug
```

### Testing

**Important:** Only run tests against the `demoDebug` variant. Do NOT run `./gradlew test` or `./gradlew connectedAndroidTest` as this executes tests against all build variants which will fail.

**Unit tests:**
```bash
# Run all local tests (screenshot tests will fail without recording first)
./gradlew testDemoDebug

# Record screenshot baselines before running tests
./gradlew recordRoborazziDemoDebug

# Verify screenshot tests
./gradlew verifyRoborazziDemoDebug

# Compare failed screenshot tests
./gradlew compareRoborazziDemoDebug
```

**Instrumented tests:**
```bash
# Run all instrumented tests
./gradlew connectedDemoDebugAndroidTest
```

**Screenshot testing notes:**
- Screenshot tests use Roborazzi and are recorded on Linux CI
- On non-Linux platforms, run `recordRoborazziDemoDebug` on main branch first before making changes
- Screenshots are stored in `modulename/src/test/screenshots`

### Performance

**Baseline profiles:**
```bash
# Generate baseline profile (use benchmark variant on AOSP emulator)
# Run BaselineProfileGenerator benchmark test, then copy results to app/src/main/baseline-prof.txt
```

**Compose compiler metrics:**
```bash
./gradlew assembleRelease -PenableComposeCompilerMetrics=true -PenableComposeCompilerReports=true
# Reports: build/compose-reports
# Metrics: build/compose-metrics
```

### Other useful commands

```bash
# Update module dependency graphs
./gradlew graphUpdate

# Lint checks (if configured)
./gradlew lint
```

## Architecture

The app follows Android's official three-layer architecture with **unidirectional data flow**:

### Data Layer (offline-first)
- **Repositories** are the public API for data access, exposing data as Kotlin Flows
- All repositories implement an "offline-first" pattern: network data is synced to local storage (Room), and local storage is the source of truth
- Data synchronization uses WorkManager via `SyncWorker` with exponential backoff for errors
- Key repositories: `OfflineFirstTopicsRepository`, `OfflineFirstNewsRepository`, `UserDataRepository`
- Data sources: Room (local database), Proto DataStore (user preferences), Retrofit (network)

**Reading data:** Always returns Flows from local storage. Errors during sync are handled separately and do not affect reads.

**Writing data:** Repositories provide suspend functions. The caller is responsible for scoping execution.

### Domain Layer (optional)
- Contains use cases with single invocable method (`operator fun invoke`)
- Combines and transforms data from multiple repositories
- Example: `GetUserNewsResourcesUseCase` combines news resources with user data to produce `UserNewsResource` objects
- Does NOT contain event handling use cases - events are handled by UI calling repositories directly

### UI Layer
- Built entirely with Jetpack Compose
- ViewModels receive Flows from use cases/repositories and transform them to UI state using `stateIn`
- UI state is modeled as sealed hierarchies (interfaces + immutable data classes)
- User interactions are passed to ViewModels as lambda expressions

**Key pattern:** Cold Flows → combine/map → `stateIn` → hot StateFlow → UI

## Modularization Strategy

The codebase is highly modularized following these principles:
- **Low coupling:** Modules are independent; changes to one have minimal impact on others
- **High cohesion:** Each module has a single, well-defined purpose

### Module Types

**`:app` module**
- Contains app scaffolding: `MainActivity`, `NiaApp`, navigation setup (`NiaNavHost`, `TopLevelDestination`)
- Depends on all `feature:` modules and required `core:` modules
- Run configuration should be set to `app` in Android Studio

**`feature:` modules** (e.g., `feature:foryou`, `feature:topic`, `feature:interests`)
- Scoped to a single feature/responsibility
- Contains UI components and ViewModels
- **Cannot depend on other feature modules** - only on `core:` modules
- Use convention plugin: `nowinandroid.android.feature`

**`core:` modules**
- `core:data` - Repositories for fetching app data from multiple sources
- `core:database` - Room database, DAOs, and migrations
- `core:datastore` - Proto DataStore for user preferences
- `core:network` - Retrofit network layer and remote data sources
- `core:model` - Model classes used throughout the app (e.g., `Topic`, `NewsResource`)
- `core:designsystem` - Material 3 design system components, theme, and icons (view via `app-nia-catalog`)
- `core:ui` - Composite UI components that depend on data models (e.g., `NewsFeed`)
- `core:common` - Shared utility classes (e.g., `NiaDispatchers`, `Result`)
- `core:domain` - Use cases
- `core:testing` - Test utilities, runners, and test doubles
- `core:screenshot-testing` - Screenshot testing utilities

**Other modules:**
- `sync:work` - WorkManager-based data synchronization
- `benchmarks` - Macrobenchmark tests and baseline profile generation
- `app-nia-catalog` - Standalone catalog app for the design system

**Module dependency rule:** `core:` modules can depend on other `core:` modules but NOT on `feature:` or `app:` modules.

### Convention Plugins

The project uses type-safe Kotlin convention plugins in `build-logic/convention/` to extract reusable build configuration:
- `nowinandroid.android.application` - Android application base config
- `nowinandroid.android.application.compose` - Compose setup for apps
- `nowinandroid.android.feature` - Feature module conventions
- `nowinandroid.android.library` - Android library base config
- `nowinandroid.android.library.compose` - Compose setup for libraries
- `nowinandroid.hilt` - Hilt dependency injection setup
- `nowinandroid.android.room` - Room database setup
- And others...

When creating new modules, apply the appropriate convention plugin instead of manually configuring Gradle.

## Testing Philosophy

**No mocking libraries are used.** Instead, the app uses test doubles that implement the same interfaces as production code:
- Test implementations of repositories provide test-only hooks for manipulation
- Real DataStore is used in instrumented tests with temporary folders
- ViewModels test against Test repositories, not mocks

This results in less brittle tests that exercise more production code.

## Dependencies

All dependencies are managed via the version catalog in `gradle/libs.versions.toml`. Use `libs.` references in build files.

**Key dependencies:**
- Hilt for dependency injection
- Room for local database
- Proto DataStore for preferences
- Retrofit + Kotlin Serialization for networking
- Jetpack Compose for UI
- Roborazzi for screenshot testing
- WorkManager for background sync
- Coil for image loading

## Build Configuration

**Product flavors:**
- `demo` - Uses static local data (for development)
- `prod` - Uses real backend server (not publicly available)

**Build types:**
- `debug` - Development builds
- `release` - Optimized builds with R8 minification
- `benchmark` - For performance testing

**Java version:** Requires JDK 17+ (checked in `settings.gradle.kts`)

**Gradle properties:**
- Parallel builds enabled
- Configuration cache enabled
- Roborazzi screenshot verification enabled by default

## Important Notes

- The app implements Material 3 with dynamic color support and dark mode
- Navigation is type-safe using Kotlin Serialization
- All UI uses adaptive layouts for different screen sizes
- The app uses Firebase for analytics, crashlytics, and performance monitoring (in prod builds)
- Baseline profiles should be regenerated when making changes affecting app startup
- Module graphs are auto-updated by CI on dependency changes (or run `graphUpdate` manually)
