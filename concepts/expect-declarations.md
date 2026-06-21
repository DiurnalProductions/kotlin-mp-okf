---
id: kmp.expect_declarations
type: concept
title: Expect Declarations
description: Declaring platform-dependent contracts in shared code that each target must fulfill with an actual implementation.
tags: [kotlin-multiplatform, expect-actual, declarations]
prerequisites:
  - kmp.abstraction_boundaries
  - kmp.shared_vs_platform
related:
  - kmp.actual_implementations
  - kmp.expect_actual_resolution
  - kmp.expect_actual_architecture
resource: https://kotlinlang.org/docs/multiplatform-expect-actual.html
timestamp: 2026-01-01
---

## Summary

An `expect` declaration in common code defines a API surface — class, function, property, or annotation — without implementation. The compiler requires a matching `actual` declaration in every platform source set. Expect/actual is the compiler-enforced linking mechanism between shared contracts and per-target code.

## Mental model

Expect is a **compile-time promise**: "Every target will provide this." It is not a runtime polymorphism mechanism. The compiler resolves expects to actuals at compile time; there is no shared vtable across platforms.

Use expect/actual when:

- The API is a small, stable primitive (e.g., `getPlatformName(): String`).
- Interface + DI would add unnecessary ceremony for a single canonical implementation.
- You need top-level functions or annotations that interfaces cannot express.

Avoid expect/actual when:

- You need multiple implementations or mocking (use interfaces).
- The API surface is large or evolving (interfaces are easier to evolve).
- You are bridging a complex platform SDK (wrap behind an interface instead).

## Example

```kotlin
// commonMain
expect class PlatformLogger() {
    fun log(message: String)
}

expect fun currentThreadName(): String

// androidMain
actual class PlatformLogger actual constructor() {
    actual fun log(message: String) = Log.d("App", message)
}
actual fun currentThreadName(): String = Thread.currentThread().name

// iosMain
actual class PlatformLogger actual constructor() {
    actual fun log(message: String) = NSLog(message)
}
actual fun currentThreadName(): String = "main" // conceptual
```

## Common mistakes

- Using expect/actual as the primary architecture pattern instead of interfaces.
- Declaring expect in a platform source set (must be in common).
- Mismatched signatures between expect and actual (compile errors per target).
- Putting business logic inside actual implementations that should live in shared code.

## Related concepts

- [Actual Implementations](actual-implementations.md)
- [Expect/Actual Resolution Rules](expect-actual-resolution.md)
- [Expect/Actual Architecture](expect-actual-architecture.md)
