---
id: kmp.structured_concurrency
type: concept
title: Structured Concurrency
description: Scopes, parent-child job relationships, and cancellation propagation consistent across KMP targets.
tags: [kotlin-multiplatform, coroutines, structured-concurrency, cancellation]
prerequisites:
  - kmp.coroutines_multiplatform
  - kmp.dispatchers_differences
related:
  - kmp.coroutines_multiplatform
  - kmp.platform_threading_model
resource: https://kotlinlang.org/docs/cancellation.html
timestamp: 2026-01-01
---

## Summary

Structured concurrency ensures coroutines have a parent scope whose lifecycle bounds child jobs. When a scope cancels, children cancel. In KMP, shared code should accept or create well-defined scopes rather than using `GlobalScope`. Platform modules bind scopes to lifecycle (Activity, ViewModel, iOS scene).

## Mental model

```
ApplicationScope (platform)
    └── ViewModelScope / LifecycleScope (platform)
            └── shared UseCase.launch { ... }  // inherits cancellation
```

Shared use cases expose `suspend fun` and let callers manage scope. If shared code must launch background work, accept a `CoroutineScope` parameter from the platform layer.

Cancellation must be **cooperative**: suspend points check cancellation; tight loops need `ensureActive()`.

## Example

```kotlin
// commonMain
class PollUseCase(private val api: Api) {
    suspend fun poll(intervalMs: Long): Nothing {
        while (true) {
            api.fetchStatus()
            delay(intervalMs)  // cancellable suspend point
        }
    }
}

// platform — caller controls scope
lifecycleScope.launch {
    pollUseCase.poll(5000)  // cancelled when lifecycle destroyed
}
```

## Common mistakes

- `GlobalScope.launch` in shared libraries (leaks work, no cancellation).
- Ignoring cancellation in `flow { }` builders and long-running loops.
- Creating orphan scopes in shared singletons.
- Different cancellation behavior per platform due to missing lifecycle binding in one app target.

## Related concepts

- [Coroutines Across Platforms](coroutines-multiplatform.md)
- [Platform Threading Model](platform-threading-model.md)
