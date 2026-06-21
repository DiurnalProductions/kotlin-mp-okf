---
id: kmp.dependency_inversion
type: concept
title: Dependency Inversion Across Platforms
description: Injecting platform implementations into shared logic at application composition roots using DI frameworks or manual wiring.
tags: [kotlin-multiplatform, dependency-injection, architecture, di]
prerequisites:
  - kmp.repository_abstraction
  - kmp.platform_modules
related:
  - kmp.clean_architecture_kmp
  - kmp.expect_actual_architecture
  - kmp.mocking_expectations
resource: https://kotlinlang.org/docs/multiplatform-connect-to-apis.html
timestamp: 2026-01-01
---

## Summary

Dependency inversion in KMP means shared code depends on abstractions defined in commonMain, and platform application modules supply concrete implementations at startup. Use Koin, Kodein, manual factories, or Swift-side injection for iOS. The composition root lives exclusively in platform modules.

## Mental model

```
commonMain defines:  interface Clock, interface Storage, class UseCase(storage)
androidApp wires:    UseCase(AndroidStorage(ctx))
iosApp wires:        UseCase(IosStorage())  // Swift or Kotlin entry
```

Never instantiate platform implementations inside shared singletons. Pass dependencies via constructor injection from the app module. For iOS, expose factory functions or use SKIE-compatible patterns for Swift interop.

## Example

```kotlin
// commonMain
class AppDependencies(
    val auth: AuthUseCase,
    val settings: SettingsUseCase
)

// androidApp
fun createDependencies(context: Context): AppDependencies {
    val storage = AndroidSecureStorage(context)
    return AppDependencies(
        auth = AuthUseCase(storage),
        settings = SettingsUseCase(storage)
    )
}
```

## Common mistakes

- Service locator in commonMain that knows about Android `Context`.
- expect/actual object graphs instead of injectable interfaces.
- Different DI graphs per platform with missing bindings (runtime crash on one target).
- iOS Swift app not wiring Kotlin dependencies (defaults to uninitialized stubs).

## Related concepts

- [Clean Architecture in KMP](clean-architecture-kmp.md)
- [Expect/Actual Architecture](expect-actual-architecture.md)
- [Mocking Expectations](mocking-expectations.md)
