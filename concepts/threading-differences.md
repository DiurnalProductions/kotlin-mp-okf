---
type: Concept
title: Threading Differences
description: "How threads, main loops, and execution models differ across JVM, Native, and JS KMP targets."
tags: [kotlin-multiplatform, threading, concurrency, platform]
prerequisites:
  - concepts/abstraction-boundaries
related:
  - concepts/platform-threading-model
  - concepts/coroutines-multiplatform
  - concepts/dispatchers-differences
resource: "https://kotlinlang.org/docs/multiplatform-mobile-understand-threading-model.html"
timestamp: 2026-01-01
---

## Summary

Each KMP target has a distinct threading model. JVM and Android use OS threads with preemptive scheduling. Kotlin/Native uses a cooperative scheduler with main and worker threads. Kotlin/JS runs on a single-threaded event loop. Shared concurrent code must respect these differences, typically via coroutines rather than raw threads.

## Mental model

Threading is a **platform fact**, not a shared assumption. Code that blocks the main thread is wrong on every platform, but what "main thread" means differs. Code that assumes `synchronized` or `ThreadLocal` works everywhere is wrong for Native and JS.

| Platform | Model | Main thread concern |
|----------|-------|---------------------|
| JVM/Android | OS threads | UI thread / main looper |
| Native | Workers + main queue | UI must run on main |
| JS | Single-threaded | Long CPU work blocks UI |

Coroutines abstract most of this, but you must still choose correct dispatchers and avoid blocking calls on Main.

## Example

```kotlin
// commonMain — use coroutines, not Thread
suspend fun loadData(): Data = withContext(Dispatchers.IO) {
    repository.fetch()  // suspends, does not block thread (when implemented correctly)
}

// Avoid in commonMain
// Thread { ... }.start()           // not portable
// synchronized(lock) { ... }       // JVM-centric
```

## Common mistakes

- Using `Thread`, `ExecutorService`, or `synchronized` in commonMain.
- Calling blocking I/O on `Dispatchers.Main`.
- Assuming `@MainThread` annotations exist on all targets.
- Sharing mutable state across threads without understanding Native memory model rules.

## Related concepts

- [Platform Threading Model](platform-threading-model.md)
- [Coroutines Across Platforms](coroutines-multiplatform.md)
- [Dispatchers Differences](dispatchers-differences.md)
