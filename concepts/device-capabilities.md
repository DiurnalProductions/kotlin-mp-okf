---
id: kmp.device_capabilities
type: concept
title: Device Capabilities
description: Accessing sensors, permissions, biometrics, and hardware features through abstractions rather than direct platform API usage in shared code.
tags: [kotlin-multiplatform, device, capabilities, permissions]
prerequisites:
  - kmp.abstraction_boundaries
  - kmp.expect_declarations
related:
  - kmp.filesystem_abstraction
  - kmp.platform_modules
resource: https://kotlinlang.org/docs/multiplatform-connect-to-apis.html
timestamp: 2026-01-01
---

## Summary

Device capabilities — camera, GPS, biometrics, Bluetooth, push notifications — are inherently platform-specific. Shared code defines capability interfaces and feature detection; platform modules implement them using OS APIs and handle permission flows in UI layers.

## Mental model

Capabilities follow a **request/result pattern** in shared code:

```
commonMain: interface BiometricAuth { suspend fun authenticate(): Result<Unit> }
androidMain: BiometricPrompt
iosMain: LAContext
desktop/jvm: stub or password fallback
```

Permission requests are UI/lifecycle concerns and belong in platform modules, not shared use cases. Shared code receives granted/denied outcomes through callbacks or suspend results.

Feature availability varies by target — web may lack biometrics; desktop may lack GPS. Model capabilities as optional with explicit fallbacks.

## Example

```kotlin
// commonMain
interface LocationProvider {
    val isAvailable: Boolean
    suspend fun currentLocation(): GeoPoint?
}

class NearbySearch(private val location: LocationProvider) {
    suspend fun search(): List<Place> {
        val point = location.currentLocation() ?: return emptyList()
        // ...
    }
}
```

## Common mistakes

- Requesting permissions from shared code without UI context.
- Assuming every target has mobile sensors.
- Exposing platform permission enum types in shared interfaces.
- Using expect/actual for entire capability subsystems instead of injectable services.

## Related concepts

- [Filesystem Abstraction](filesystem-abstraction.md)
- [Platform Modules](platform-modules.md)
