---
id: kmp.platform_specific_overrides
type: concept
title: Platform-Specific Overrides
description: Targeting individual platforms or platform families within the expect/actual and source set hierarchy.
tags: [kotlin-multiplatform, expect-actual, platform-specific]
prerequisites:
  - kmp.expect_actual_resolution
related:
  - kmp.hierarchical_source_sets
  - kmp.actual_implementations
resource: https://kotlinlang.org/docs/multiplatform-hierarchy.html
timestamp: 2026-01-01
---

## Summary

Platform-specific overrides allow fine-grained control when behavior differs not just between Android and iOS, but between simulator and device, or between JVM and Android. Intermediate source sets (`appleMain`, `jvmAndAndroidMain`) and target-specific source sets (`iosArm64Main`) enable override without duplicating entire implementations.

## Mental model

The source set hierarchy is an **inheritance tree for implementations**. More specific source sets override more general ones. Platform-specific overrides are the KMP equivalent of overriding a method in a subclass — the most specific applicable implementation wins for that compilation.

Override scenarios:

- iOS simulator vs device (different linking or APIs).
- Android vs desktop JVM (both JVM but different APIs).
- Apple vs Linux Native (shared `nativeMain` with Apple-specific `appleMain`).

Prefer intermediate source sets over copy-paste across `iosArm64Main` and `iosSimulatorArm64Main`.

## Example

```kotlin
// nativeMain — default for all Native targets
actual fun isDebugBuild(): Boolean = Platform.isDebugBinary

// iosMain — override for all iOS variants
actual fun isDebugBuild(): Boolean = IS_DEBUG_BUILD  // from Xcode config

// androidMain
actual fun isDebugBuild(): Boolean = BuildConfig.DEBUG
```

## Common mistakes

- Creating separate actuals in `iosArm64Main` and `iosSimulatorArm64Main` when `iosMain` or `appleMain` suffices.
- Overriding in the wrong source set level, causing ambiguous resolution.
- Platform-specific overrides for logic that should be identical (unnecessary divergence).
- Using overrides to smuggle platform imports into effectively shared code paths.

## Related concepts

- [Hierarchical Source Sets](hierarchical-source-sets.md)
- [Actual Implementations](actual-implementations.md)
