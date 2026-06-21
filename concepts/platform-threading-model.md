---
id: kmp.platform_threading_model
type: concept
title: Platform Threading Model
description: Deep comparison of JVM threads, Kotlin/Native workers, and JavaScript single-threaded execution as they affect KMP design.
tags: [kotlin-multiplatform, threading, jvm, native, js]
prerequisites:
  - kmp.threading_differences
related:
  - kmp.memory_model_differences
  - kmp.dispatchers_differences
  - kmp.jvm_vs_native_runtime
resource: https://kotlinlang.org/docs/native-migration-guide.html
timestamp: 2026-01-01
---

## Summary

Each KMP target's threading model affects how shared mutable state, coroutine cancellation, and FFI boundaries behave. JVM offers mature preemptive threading. Native uses a dedicated runtime with main-thread UI rules and evolving memory model semantics. JS is single-threaded with cooperative multitasking via the event loop.

## Mental model

```
JVM/Android:  many OS threads, synchronized, ThreadLocal available
Native:       main thread for UI, workers for background, strict sharing rules (historically)
JS:           one thread, no true parallelism in standard KMP JS
```

When shared code runs on Native and is called from Swift, thread confinement matters — UI callbacks may arrive on main while coroutine continuations resume on workers. Use `Dispatchers.Main` explicitly when updating UI-bound state from shared code called by platform UI.

Design shared state as **immutable** or **coroutine-confined** to avoid cross-thread data races without platform-specific locks.

## Example

```kotlin
// commonMain — confine mutable state to a single-thread context
class Counter {
    private val _value = MutableStateFlow(0)
    val value: StateFlow<Int> = _value.asStateFlow()
    fun increment() { _value.update { it + 1 } }  // call from appropriate context
}
```

## Common mistakes

- Sharing mutable objects across threads on Native without understanding freeze/transfer rules.
- Expecting parallel coroutine CPU work on JS to improve performance.
- Calling shared Kotlin code from wrong Swift thread without dispatcher hop.
- Using JVM concurrency primitives in mental models for all targets.

## Related concepts

- [Memory Model Differences](memory-model-differences.md)
- [Dispatchers Differences](dispatchers-differences.md)
- [JVM vs Native Runtime](jvm-vs-native-runtime.md)
