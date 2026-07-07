---
type: Concept
title: Shared vs Platform Code
description: The foundational split between portable business logic in common source sets and platform-specific implementations in target source sets.
tags: [kotlin-multiplatform, shared-code, architecture, fundamentals]
related:
  - concepts/compilation-targets
  - concepts/abstraction-boundaries
  - concepts/shared-module
resource: "https://kotlinlang.org/docs/multiplatform-discover-project.html"
timestamp: 2026-01-01
---

## Summary

Kotlin Multiplatform organizes code into two conceptual zones: **shared code** that compiles for every target and **platform code** that exists only on specific targets. Shared code holds business logic, domain models, and abstractions. Platform code provides implementations, UI shells, and integrations with OS APIs. The default design posture is to maximize shared code and minimize platform code.

## Mental model

Think of KMP as a **logic-sharing layer**, not a UI-sharing layer. Shared code is the contract and the brain; platform code is the hands and eyes. Every line of code should answer: "Can this compile and behave correctly on all targets?" If yes, it belongs in shared code. If no, it belongs behind an abstraction in platform code.

```
┌─────────────────────────────────────┐
│           shared (commonMain)       │
│  domain · use cases · interfaces    │
└──────────────┬──────────────────────┘
               │ depends on abstractions
    ┌──────────┼──────────┬──────────┐
    ▼          ▼          ▼          ▼
 androidMain iosMain   jvmMain    jsMain
 (actuals)  (actuals) (actuals)  (actuals)
```

Shared code must never import platform SDKs (`android.*`, `UIKit`, `java.io.File` on Android-only paths). Platform code may import shared code; the reverse is forbidden except through expect/actual or injected interfaces.

## Example

```kotlin
// commonMain — shared interface
interface LocationProvider {
    suspend fun currentLocation(): GeoPoint
}

// commonMain — shared use case (pure logic)
class FindNearbyStores(private val location: LocationProvider) {
    suspend fun execute(radiusKm: Double): List<Store> { /* ... */ }
}

// androidMain — platform implementation
actual class PlatformLocationProvider : LocationProvider { /* FusedLocationProvider */ }

// iosMain — platform implementation
actual class PlatformLocationProvider : LocationProvider { /* CLLocationManager */ }
```

## Common mistakes

- Putting Android `Context` or iOS `UIViewController` references into shared code.
- Duplicating business logic in each platform module instead of lifting it to common.
- Treating KMP as "write once, run everywhere" for UI — it is "write logic once, implement per platform."
- Making shared code depend on platform libraries "just for convenience."

## Related concepts

- [Compilation Targets](compilation-targets.md)
- [Abstraction Boundaries](abstraction-boundaries.md)
- [Shared Module](shared-module.md)
