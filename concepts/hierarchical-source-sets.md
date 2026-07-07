---
type: Concept
title: Hierarchical Source Sets
description: "The commonMain, androidMain, iosMain, jsMain hierarchy and intermediate sets like appleMain and nativeMain that reduce duplication across related targets."
tags: [kotlin-multiplatform, source-sets, commonMain, androidMain, iosMain]
prerequisites:
  - concepts/source-sets
  - concepts/shared-module
related:
  - concepts/platform-specific-overrides
  - concepts/expect-actual-resolution
resource: "https://kotlinlang.org/docs/multiplatform-hierarchy.html"
timestamp: 2026-01-01
---

## Summary

KMP uses a hierarchical source set tree. `commonMain` is the root shared by all targets. Platform sets (`androidMain`, `jvmMain`, `jsMain`) and intermediate sets (`nativeMain`, `appleMain`, `iosMain`) specialize behavior for target families. The hierarchy is configured explicitly or via the default template matching declared targets.

## Mental model

```
                    commonMain
           ┌──────────┼──────────┬──────────┐
           ▼          ▼          ▼          ▼
      androidMain  jvmMain   jsMain   nativeMain
                                      ┌────┴────┐
                                      ▼         ▼
                                  appleMain  linuxMain
                                      │
                                  ┌───┴───┐
                                  ▼       ▼
                              iosMain  macosMain
```

Code in `commonMain` compiles everywhere. Code in `appleMain` compiles for iOS and macOS but not Android. Code in `iosMain` compiles only for iOS targets. This hierarchy prevents copy-paste between `iosArm64Main` and `iosSimulatorArm64Main`.

## Example

```kotlin
// commonMain/UuidGenerator.kt — shared interface
interface UuidGenerator { fun generate(): String }

// appleMain/AppleUuid.kt — shared by all Apple targets
class AppleUuid : UuidGenerator { /* NSUUID */ }

// androidMain/AndroidUuid.kt
class AndroidUuid : UuidGenerator { /* java.util.UUID */ }
```

Source set locations:

| Source set | Typical contents |
|------------|------------------|
| `commonMain` | Domain, interfaces, use cases |
| `androidMain` | Android SDK wrappers, actuals |
| `iosMain` | iOS-specific interop, UIKit bridges |
| `jsMain` | Browser APIs, DOM interop |
| `commonTest` | Shared unit tests |

## Common mistakes

- Putting iOS code in `nativeMain` when it uses Apple-only APIs (belongs in `appleMain` or `iosMain`).
- Not enabling hierarchical structure in Gradle (leads to flat, duplicated source sets).
- Assuming `androidMain` and `jvmMain` are interchangeable (Android has unique APIs).
- Creating custom intermediate source sets without clear target-family boundaries.

## Related concepts

- [Platform-Specific Overrides](platform-specific-overrides.md)
- [Expect/Actual Resolution Rules](expect-actual-resolution.md)
