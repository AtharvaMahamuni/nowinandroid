# Now in Android - Learning Checklist

Track your progress as you learn Android development concepts from this app. Mark items with `[x]` as you complete them.

---

## 🏗️ Architecture & Design Patterns

- [ ] **Clean Architecture** - Three-layer architecture (Data, Domain, UI) with unidirectional data flow
  - Key files: `core/data/`, `core/domain/`, `feature/*/`

- [ ] **Offline-First Pattern** - Local storage as source of truth, network data synced in background
  - Key files: `core/data/repository/OfflineFirstNewsRepository.kt`, `core/data/repository/OfflineFirstTopicsRepository.kt`

- [ ] **Repository Pattern** - Abstracted data access with Flow-based APIs
  - Key files: `core/data/repository/`

- [ ] **Use Cases (Domain Layer)** - Combining multiple repositories for complex business logic
  - Key files: `core/domain/GetFollowableTopicsUseCase.kt`, `core/domain/GetUserNewsResourcesUseCase.kt`

- [ ] **MVVM Pattern** - ViewModels transforming Flows to UI state using `stateIn`
  - Key files: `feature/foryou/ForYouViewModel.kt`, `feature/bookmarks/BookmarksViewModel.kt`

---

## 🎨 Jetpack Compose

- [ ] **100% Compose UI** - No XML layouts, entirely declarative UI
  - Key files: `app/src/main/kotlin/com/google/samples/apps/nowinandroid/ui/NiaApp.kt`

- [ ] **State Management** - Cold Flows → `stateIn` → Hot StateFlows pattern
  - Key files: Any ViewModel file (e.g., `feature/foryou/ForYouViewModel.kt`)

- [ ] **Sealed UI State** - Type-safe state modeling with sealed interfaces
  - Key files: `feature/foryou/ForYouViewModel.kt` (see `NewsFeedUiState`)

- [ ] **Adaptive Layouts** - Window size classes and responsive design
  - Key files: `core/ui/`, `app/src/main/kotlin/com/google/samples/apps/nowinandroid/ui/NiaApp.kt`

- [ ] **Material Design 3** - Dynamic colors, light/dark themes, edge-to-edge rendering
  - Key files: `core/designsystem/src/main/kotlin/com/google/samples/apps/nowinandroid/core/designsystem/theme/Theme.kt`

---

## 🧩 Modularization

- [ ] **Multi-Module Architecture** - Feature modules, core modules, separation of concerns
  - Explore: Project structure with `feature:*` and `core:*` modules

- [ ] **Module Dependency Rules** - Low coupling, high cohesion principles
  - Key files: Module `build.gradle.kts` files

- [ ] **Convention Plugins** - Reusable Gradle configuration with type-safe Kotlin DSL
  - Key files: `build-logic/convention/src/main/kotlin/`

- [ ] **Version Catalogs** - Centralized dependency management in TOML
  - Key files: `gradle/libs.versions.toml`

---

## 💉 Dependency Injection

- [ ] **Hilt Setup** - Modern DI with scoped components
  - Key files: `app/src/main/kotlin/com/google/samples/apps/nowinandroid/NiaApplication.kt`, `app/src/main/kotlin/com/google/samples/apps/nowinandroid/MainActivity.kt`

- [ ] **Module Binding** - Abstract modules binding interfaces to implementations
  - Key files: `core/data/src/main/kotlin/com/google/samples/apps/nowinandroid/core/data/di/DataModule.kt`

- [ ] **Custom Qualifiers** - Injectable CoroutineDispatchers with `@Dispatcher`
  - Key files: `core/common/src/main/kotlin/com/google/samples/apps/nowinandroid/core/common/network/di/DispatchersModule.kt`

- [ ] **Lazy Injection** - `dagger.Lazy<T>` for expensive components
  - Key files: `app/src/main/kotlin/com/google/samples/apps/nowinandroid/MainActivity.kt:62` (JankStats)

---

## 🗄️ Data Layer

- [ ] **Room Database** - DAOs, entities, auto-migrations, FTS search
  - Key files: `core/database/src/main/kotlin/com/google/samples/apps/nowinandroid/core/database/NiaDatabase.kt`

- [ ] **Proto DataStore** - Type-safe preferences with Protocol Buffers
  - Key files: `core/datastore/`

- [ ] **Retrofit + Serialization** - Modern networking with Kotlin Serialization
  - Key files: `core/network/`

- [ ] **Change-List Sync** - Incremental sync strategy to reduce network calls
  - Key files: `core/data/repository/OfflineFirstNewsRepository.kt` (see `sync()` method)

- [ ] **WorkManager** - Background data synchronization with retry logic
  - Key files: `sync/work/src/main/kotlin/com/google/samples/apps/nowinandroid/sync/workers/SyncWorker.kt`

- [ ] **Result Wrapper** - Error handling with sealed `Result<T>` class
  - Key files: `core/common/src/main/kotlin/com/google/samples/apps/nowinandroid/core/common/result/Result.kt`

---

## 🧪 Testing

- [ ] **Test Doubles (Not Mocks)** - Fake implementations instead of mocking frameworks
  - Key files: `core/testing/src/main/kotlin/com/google/samples/apps/nowinandroid/core/testing/repository/`

- [ ] **Compose Testing** - UI testing with `createComposeRule()`
  - Key files: `app/src/androidTest/` or feature module tests

- [ ] **Screenshot Testing** - Roborazzi for visual regression testing
  - Key files: `core/screenshot-testing/`, test files with `@GraphicsMode(NATIVE)`

- [ ] **Flow Testing** - Turbine library for Flow assertions
  - Key files: `core/common/src/test/kotlin/com/google/samples/apps/nowinandroid/core/common/result/ResultKtTest.kt`

- [ ] **Variant-Specific Tests** - Only test against `demoDebug` variant
  - Run: `./gradlew testDemoDebug`

---

## ⚡ Performance

- [ ] **Baseline Profiles** - App startup optimization
  - Key files: `app/src/main/baseline-prof.txt`, `benchmarks/`

- [ ] **JankStats** - Real-time performance monitoring
  - Key files: `app/src/main/kotlin/com/google/samples/apps/nowinandroid/MainActivity.kt:62`

- [ ] **Compose Compiler Metrics** - Analyzing composable performance
  - Run: `./gradlew assembleRelease -PenableComposeCompilerMetrics=true -PenableComposeCompilerReports=true`

- [ ] **StrictMode** - Detecting main thread violations in debug builds
  - Key files: `app/src/main/kotlin/com/google/samples/apps/nowinandroid/NiaApplication.kt`

- [ ] **Lazy Initialization** - Deferred loading of heavy components
  - Key files: `app/src/main/kotlin/com/google/samples/apps/nowinandroid/MainActivity.kt` (lazyStats)

---

## 🔧 Build System

- [ ] **Gradle Convention Plugins** - Custom plugins in `build-logic/`
  - Key files: `build-logic/convention/src/main/kotlin/`

- [ ] **Build Variants** - Product flavors (demo/prod) and build types
  - Key files: `app/build.gradle.kts` (productFlavors)

- [ ] **Configuration Cache** - Faster builds with caching
  - Key files: `gradle.properties`

- [ ] **Build Performance** - Parallel builds, JVM optimization
  - Key files: `gradle.properties`

---

## 🎯 Modern Android APIs

- [ ] **Splash Screen API** - AndroidX Core SplashScreen with conditional display
  - Key files: `app/src/main/kotlin/com/google/samples/apps/nowinandroid/MainActivity.kt:79`

- [ ] **Edge-to-Edge** - Inset handling, transparent system bars
  - Key files: `app/src/main/kotlin/com/google/samples/apps/nowinandroid/MainActivity.kt:115`

- [ ] **Type-Safe Navigation** - Kotlin Serialization for navigation arguments
  - Key files: `app/src/main/kotlin/com/google/samples/apps/nowinandroid/navigation/`

- [ ] **Chrome Custom Tabs** - In-app browser experience
  - Key files: Search for `CustomTabsIntent` usage

- [ ] **Coil Image Loading** - Async image loading with caching
  - Key files: `core/designsystem/` (ImageLoader factory)

---

## 📊 Advanced Patterns

- [ ] **Kotlin Coroutines & Flow** - Async operations, Flow operators, `combine`, `stateIn`
  - Key files: Any ViewModel or Repository

- [ ] **Sealed Hierarchies** - Exhaustive when statements for type safety
  - Key files: `core/common/src/main/kotlin/com/google/samples/apps/nowinandroid/core/common/result/Result.kt`

- [ ] **Extension Functions** - Custom DSL-like APIs
  - Key files: `core/ui/` (LazyGridScope extensions)

- [ ] **Analytics Abstraction** - Testable analytics with `AnalyticsHelper` interface
  - Key files: `core/analytics/`

- [ ] **Network Monitoring** - Real-time connectivity state tracking
  - Key files: `core/data/src/main/kotlin/com/google/samples/apps/nowinandroid/core/data/util/NetworkMonitor.kt`

- [ ] **Time Zone Monitoring** - System time zone change detection
  - Key files: `core/data/src/main/kotlin/com/google/samples/apps/nowinandroid/core/data/util/TimeZoneMonitor.kt`

- [ ] **Tracing** - Performance profiling with `androidx.tracing`
  - Key files: Search for `trace("...")` usage

---

## 📝 Progress Summary

**Total Concepts:** 50
**Completed:** 0
**Remaining:** 50

---

## 🎓 Learning Path Recommendations

### For Beginners (Start Here):
1. 100% Compose UI
2. State Management
3. Sealed UI State
4. Material Design 3
5. MVVM Pattern

### For Intermediate Developers:
1. Repository Pattern
2. Offline-First Pattern
3. Room Database
4. Hilt Setup
5. Kotlin Coroutines & Flow

### For Advanced Developers:
1. Multi-Module Architecture
2. Convention Plugins
3. Change-List Sync
4. Test Doubles
5. Baseline Profiles

---

## 💡 Tips for Learning

1. **Read the code first** - Open the key files and understand the implementation
2. **Run the app** - Use `./gradlew installDemoDebug` to see it in action
3. **Make small changes** - Modify code to see how things work
4. **Run tests** - Execute `./gradlew testDemoDebug` after changes
5. **Ask questions** - Use this checklist to guide your learning discussions

---

## 📚 Additional Resources

- Official Documentation: https://developer.android.com/
- Now in Android Guide: https://github.com/android/nowinandroid
- Architecture Guide: https://developer.android.com/topic/architecture
- Jetpack Compose: https://developer.android.com/jetpack/compose

---

**Last Updated:** 2025-10-18
