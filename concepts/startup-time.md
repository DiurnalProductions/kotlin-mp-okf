---
type: Concept
title: Startup Time Considerations
description: "Cold start, framework initialization, binary size, and lazy loading trade-offs across KMP targets."
tags: [kotlin-multiplatform, performance, startup, binary-size]
prerequisites:
  - concepts/jvm-vs-native-runtime
  - concepts/native-compilation-costs
related:
  - concepts/transitive-constraints
  - concepts/shared-dependencies
resource: "https://kotlinlang.org/docs/native-overview.html"
timestamp: 2026-01-01
---

## Summary

Startup time is affected by binary size, static initialization, dependency graph loading, and platform integration overhead. iOS apps pay cost to load the Kotlin/Native framework at launch. Android pays DEX load and class initialization. Shared module design should defer heavy work and avoid eager singleton initialization.

## Mental model

Startup path:

```
App launch → load KMP framework/runtime → static init → DI graph → first frame
```

Reduce startup impact by:

- Lazy DI module initialization.
- Deferring database and network client creation until first use.
- Minimizing `commonMain` dependency count (smaller Native binary).
- Avoiding heavy work in `init` blocks and object constructors.

## Example

```kotlin
// commonMain — lazy initialization
class RepositoryFactory(private val provider: () -> HttpClient) {
  private val client by lazy { provider() }
}
```

## Common mistakes

- Eager `object` singletons that open databases at import time.
- Massive transitive dependency trees increasing link size on iOS.
- Running full sync on `Application.onCreate` before UI.
- Static framework linking entire unused KMP surface into iOS binary.

## Related concepts

- [Transitive Constraints](transitive-constraints.md)
- [Shared Dependencies](shared-dependencies.md)
