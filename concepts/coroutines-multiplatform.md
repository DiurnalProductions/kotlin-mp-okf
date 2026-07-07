---
type: Concept
title: Coroutines Across Platforms
description: Using kotlinx.coroutines as the shared concurrency primitive that compiles and behaves consistently across KMP targets.
tags: [kotlin-multiplatform, coroutines, concurrency, async]
prerequisites:
  - concepts/threading-differences
related:
  - concepts/dispatchers-differences
  - concepts/structured-concurrency
  - concepts/platform-threading-model
resource: "https://github.com/Kotlin/kotlinx.coroutines/blob/master/README.md"
timestamp: 2026-01-01
---

## Summary

`kotlinx-coroutines-core` is the standard multiplatform concurrency library for KMP. Suspend functions, Flow, and coroutine scopes work across JVM, Native, and JS. Shared business logic should express async work as suspend functions and Flows, not platform callbacks or threads.

## Mental model

Coroutines are the **lingua franca of KMP async**. A suspend function in `commonMain` compiles to continuation-passing style on each platform. Callers on Android use `lifecycleScope`; on iOS, coroutines integrate via `dispatch_async` bridges; the shared logic itself is identical.

Key rules for shared coroutine code:

- Prefer `suspend fun` over callback interfaces.
- Use `Flow` for streams of values (with cold/hot semantics understood).
- Never block inside suspend functions (`Thread.sleep`, blocking IO without dispatcher).
- Propagate cancellation — check `isActive` in long loops.

## Example

```kotlin
// commonMain
class SyncRepository(private val api: Api, private val db: LocalStore) {
    suspend fun sync(): SyncResult {
        val remote = api.fetchChanges()
        db.apply(remote)
        return SyncResult.Success(remote.count)
    }

    fun observeChanges(): Flow<List<Item>> = db.observeAll()
}
```

## Common mistakes

- Mixing RxJava or platform callbacks in shared domain layer.
- Using `GlobalScope` in shared code (no structured parent).
- Blocking calls inside coroutines without `withContext(Dispatchers.IO)`.
- Assuming `runBlocking` is acceptable in production shared code (test-only pattern).

## Related concepts

- [Dispatchers Differences](dispatchers-differences.md)
- [Structured Concurrency](structured-concurrency.md)
- [Platform Threading Model](platform-threading-model.md)
