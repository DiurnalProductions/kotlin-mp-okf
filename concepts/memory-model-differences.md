---
type: Concept
title: Memory Model Differences
description: "Shared mutable state, object freezing, and concurrency rules that differ across JVM, Native, and JS KMP targets."
tags: [kotlin-multiplatform, memory-model, concurrency, native]
prerequisites:
  - concepts/compilation-targets
  - concepts/threading-differences
related:
  - concepts/platform-threading-model
  - concepts/jvm-vs-native-runtime
resource: "https://kotlinlang.org/docs/native-migration-guide.html"
timestamp: 2026-01-01
---

## Summary

Memory and concurrency semantics differ across KMP targets. JVM offers well-understood happens-before via synchronized and volatile. Kotlin/Native historically required explicit freezing of objects shared across threads; newer memory models relax some constraints but immutable and confinement patterns remain best practice. JS is single-threaded with no true shared memory parallelism.

## Mental model

Design shared state as:

1. **Immutable** — preferred default.
2. **Coroutine-confined** — mutable state owned by a single coroutine context.
3. **Actor/isolated** — Channel or Mutex-guarded mutation.

Avoid sharing mutable objects across Native threads without understanding current Kotlin/Native memory rules. When exporting Kotlin objects to Swift, object lifetime and thread affinity matter.

## Example

```kotlin
// commonMain — immutable update pattern
data class AppState(val count: Int, val items: List<Item>)

fun AppState.increment(): AppState = copy(count = count + 1)
```

## Common mistakes

- Global mutable singletons modified from multiple dispatchers on Native.
- Passing mutable Kotlin collections to Swift without defensive copy.
- Assuming `@Volatile` JVM semantics exist identically on Native.
- Concurrent HashMap-style patterns without multiplatform-safe primitives.

## Related concepts

- [Platform Threading Model](platform-threading-model.md)
- [JVM vs Native Runtime](jvm-vs-native-runtime.md)
