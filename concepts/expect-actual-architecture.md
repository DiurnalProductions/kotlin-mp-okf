---
type: Concept
title: Expect/Actual Architecture
description: "Architectural guidance for when expect/actual is appropriate versus interfaces, and how to avoid making it the center of your design."
tags: [kotlin-multiplatform, expect-actual, architecture]
prerequisites:
  - concepts/expect-actual-resolution
  - concepts/abstraction-boundaries
related:
  - concepts/dependency-inversion
  - concepts/repository-abstraction
resource: "https://kotlinlang.org/docs/multiplatform-connect-to-apis.html"
timestamp: 2026-01-01
---

## Summary

Expect/actual is a **controlled escape hatch** for platform primitives, not a substitute for clean architecture. Use it sparingly for low-level building blocks (dispatchers, logging hooks, build flags). Domain logic, data access, and service orchestration belong behind interfaces in shared code with platform-provided implementations injected at the application edge.

## Mental model

Architecture layers in KMP:

```
┌──────────────────────────────────────┐
│ Domain & use cases (commonMain)      │  ← no expect/actual
├──────────────────────────────────────┤
│ Repository interfaces (commonMain)   │  ← interfaces, not expect
├──────────────────────────────────────┤
│ Platform services (android/ios)    │  ← interface implementations
├──────────────────────────────────────┤
│ Primitives (expect/actual, rare)     │  ← Dispatchers.Main, etc.
└──────────────────────────────────────┘
```

Expect/actual creates **compile-time coupling** between common and every platform source set. Each new expect increases the cost of adding targets and writing tests. Interfaces create **runtime/test-time flexibility** with a single common declaration.

Rule of thumb: if you would mock it in a test, use an interface. If it is a fundamental platform fact, consider expect/actual.

## Example

```kotlin
// Good — interface for testable service
interface Analytics { fun track(event: String) }
class CheckoutUseCase(private val analytics: Analytics) { /* ... */ }

// Acceptable — expect for platform primitive
expect val isMainThread: Boolean

// Poor — expect for entire data layer
expect class Database()  // prefer interface + platform impl
```

## Common mistakes

- Building the entire platform bridge layer with expect/actual classes.
- Expect/actual for repositories, network clients, or databases.
- Inability to unit test shared logic because dependencies are expect declarations.
- Adding expects for convenience when a multiplatform library already abstracts the concern.

## Related concepts

- [Dependency Inversion Across Platforms](dependency-inversion.md)
- [Repository Abstraction](repository-abstraction.md)
