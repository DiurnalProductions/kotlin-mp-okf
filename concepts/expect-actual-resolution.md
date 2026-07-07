---
type: Concept
title: Expect/Actual Resolution Rules
description: Compiler rules governing how expect declarations in common code are matched to actual implementations per target.
tags: [kotlin-multiplatform, expect-actual, compilation]
prerequisites:
  - concepts/expect-declarations
  - concepts/actual-implementations
related:
  - concepts/platform-specific-overrides
  - concepts/hierarchical-source-sets
resource: "https://kotlinlang.org/docs/multiplatform-expect-actual.html#rules-for-expected-and-actual-declarations"
timestamp: 2026-01-01
---

## Summary

The Kotlin compiler resolves each `expect` declaration to exactly one `actual` per compilation. Signatures must match (modulo platform type mapping). Actuals must reside in source sets that contribute to the target being compiled. Hierarchical source sets allow intermediate actuals shared across related targets.

## Mental model

Resolution is **static and per-target**. When compiling for iOS, the compiler looks in `iosMain` and its dependencies (including `appleMain`, `nativeMain`, `commonMain`) for actuals. Missing actuals are compile errors, not runtime failures.

Key rules:

1. **One-to-one**: Each expect has exactly one actual per target.
2. **Signature parity**: Return types, parameters, visibility, and modality must align.
3. **Location**: Expect in common; actual in platform or intermediate source sets.
4. **Inheritance**: `expect class` / `actual class` must share the same inheritance structure.
5. **Annotations**: `expect annotation` requires matching `actual annotation` on each target.

Intermediate source sets (e.g., `appleMain` for iOS + macOS) reduce duplication when multiple Native Apple targets share behavior.

## Example

```kotlin
// commonMain
expect fun formatBytes(count: Long): String

// appleMain (shared by iosArm64, iosSimulatorArm64, macosArm64)
actual fun formatBytes(count: Long): String = NSByteCountFormatter().stringFromByteCount(count)

// androidMain
actual fun formatBytes(count: Long): String = Formatter.formatFileSize(context, count)
```

When compiling `iosArm64`, the compiler uses `appleMain`'s actual. When compiling `android`, it uses `androidMain`'s actual.

## Common mistakes

- Declaring multiple actuals for the same expect in source sets that both contribute to one target.
- Expect/actual signature drift after refactoring (rename one side, forget the other).
- Placing actual in `commonMain` (illegal — common cannot contain actual).
- Assuming runtime fallback if actual is missing (there is none — build fails).

## Related concepts

- [Platform-Specific Overrides](platform-specific-overrides.md)
- [Hierarchical Source Sets](hierarchical-source-sets.md)
