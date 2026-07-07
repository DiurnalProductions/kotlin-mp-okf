---
type: Concept
title: Dispatchers Differences
description: "How Dispatchers.Main, Dispatchers.IO, and Dispatchers.Default map to platform-specific thread pools and executors."
tags: [kotlin-multiplatform, coroutines, dispatchers, threading]
prerequisites:
  - concepts/coroutines-multiplatform
  - concepts/threading-differences
related:
  - concepts/platform-threading-model
  - concepts/structured-concurrency
resource: "https://kotlinlang.org/docs/multiplatform-mobile-understand-threading-model.html"
timestamp: 2026-01-01
---

## Summary

Kotlin coroutine dispatchers route work to appropriate threads per platform. `Dispatchers.Main` maps to the UI thread on Android and iOS. `Dispatchers.IO` and `Dispatchers.Default` use platform-appropriate worker pools. Shared code uses the same dispatcher names; the runtime resolves them per target.

## Mental model

Dispatchers are **semantic labels**, not identical thread pools:

| Dispatcher | Intended use | Platform mapping (conceptual) |
|------------|--------------|-------------------------------|
| Main | UI updates, fast work | Android main looper, iOS main queue, JS microtask |
| IO | Blocking I/O | Shared thread pool (size varies) |
| Default | CPU-bound work | Core-count-based pool |

On Kotlin/Native, avoid freezing violations when passing objects between dispatchers (legacy concern mitigated in newer memory models, but still design carefully). On JS, all dispatchers effectively run on the single thread.

## Example

```kotlin
// commonMain
suspend fun refreshUiState(repo: Repository): UiState {
    val data = withContext(Dispatchers.IO) { repo.loadHeavy() }
    return withContext(Dispatchers.Main) {
        UiState.from(data)  // if touching UI-bound state
    }
}
```

## Common mistakes

- Performing network or disk I/O on `Dispatchers.Main`.
- CPU-intensive parsing on Main (janks UI on all platforms).
- Creating custom thread pools in commonMain instead of using dispatchers.
- Assuming `Dispatchers.IO` is unbounded on Native (resource limits differ).

## Related concepts

- [Platform Threading Model](platform-threading-model.md)
- [Structured Concurrency](structured-concurrency.md)
