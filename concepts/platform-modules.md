---
id: kmp.platform_modules
type: concept
title: Platform Modules
description: Target-specific application or library modules that consume the shared KMP module and host UI, DI wiring, and platform integrations.
tags: [kotlin-multiplatform, modules, android, ios, platform]
prerequisites:
  - kmp.source_sets
  - kmp.shared_module
related:
  - kmp.actual_implementations
  - kmp.dependency_inversion
  - kmp.shared_logic_vs_ui
resource: https://kotlinlang.org/docs/multiplatform-discover-project.html
timestamp: 2026-01-01
---

## Summary

Platform modules are Gradle subprojects for each delivery target: `androidApp`, iOS Xcode project embedding a KMP framework, `jvmApp`, or `jsApp`. They depend on the shared module, instantiate platform implementations of shared interfaces, and host platform UI. They should remain thin — wiring and presentation, not business logic.

## Mental model

Platform modules are **composition roots**. They are where the object graph is assembled: which `SecureStorage`, `HttpClient`, and `Database` implementation gets injected into shared use cases. They are also where lifecycle hooks live (Activity, Application, UIViewController delegate).

```
androidApp/
  MainActivity.kt        // UI entry
  AndroidModule.kt       // Koin/Hilt bindings
  depends on :shared

iosApp/
  iOSApp.swift           // SwiftUI entry
  Framework from :shared // linked as .framework
```

Platform modules may contain platform-only libraries (AndroidX, Swift interop bridges) that never appear in shared code.

## Example

```kotlin
// androidApp — DI wiring
fun androidModule(context: Context) = module {
    single<SecureStorage> { AndroidSecureStorage(context) }
    single { AuthRepository(get()) }
}

// iosApp — conceptual Swift entry
// @main struct App: SwiftUI.App { ... inject IosSecureStorage ... }
```

## Common mistakes

- Reimplementing use cases in platform modules instead of calling shared code.
- Passing Android `Context` deep into shared modules via constructors defined in common.
- Platform modules that grow into "second shared modules" with duplicated logic.
- Forgetting to export the shared framework correctly for iOS (embed and sign).

## Related concepts

- [Actual Implementations](actual-implementations.md)
- [Dependency Inversion Across Platforms](dependency-inversion.md)
- [Shared Logic vs Platform UI](shared-logic-vs-ui.md)
