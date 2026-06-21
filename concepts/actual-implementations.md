---
id: kmp.actual_implementations
type: concept
title: Actual Implementations
description: Platform-specific implementations that fulfill expect declarations declared in shared common code.
tags: [kotlin-multiplatform, expect-actual, implementations]
prerequisites:
  - kmp.expect_declarations
related:
  - kmp.expect_actual_resolution
  - kmp.platform_specific_overrides
  - kmp.platform_modules
resource: https://kotlinlang.org/docs/multiplatform-expect-actual.html
timestamp: 2026-01-01
---

## Summary

`actual` declarations live in platform source sets (`androidMain`, `iosMain`, etc.) and provide the concrete implementation for a corresponding `expect` in `commonMain`. Every target compilation must supply exactly one matching actual for each expect, with compatible signatures.

## Mental model

Actual is the **per-target fulfillment** of a shared contract. Think of it as dependency injection performed by the compiler rather than a runtime container. Each platform module "plugs in" its version when that target is compiled.

Actual implementations may freely use platform SDKs because they live in platform source sets. Keep actual bodies thin — delegate to platform helpers or wrap SDK calls, but keep logic in shared code when possible.

## Example

```kotlin
// commonMain
expect object AppInfo {
    val versionName: String
    val bundleId: String
}

// androidMain
actual object AppInfo {
    actual val versionName: String
        get() = BuildConfig.VERSION_NAME
    actual val bundleId: String
        get() = BuildConfig.APPLICATION_ID
}

// iosMain
actual object AppInfo {
    actual val versionName: String
        get() = NSBundle.mainBundle.infoDictionary["CFBundleShortVersionString"] as String
    actual val bundleId: String
        get() = NSBundle.mainBundle.bundleIdentifier!!
}
```

## Common mistakes

- Implementing business rules in actual bodies instead of shared code.
- Forgetting actual for a newly added compilation target (build fails at link time).
- Using different semantics per platform without documenting the behavioral contract.
- Duplicating actual logic across similar targets (use `appleMain` or intermediate source sets).

## Related concepts

- [Expect/Actual Resolution Rules](expect-actual-resolution.md)
- [Platform-Specific Overrides](platform-specific-overrides.md)
- [Platform Modules](platform-modules.md)
