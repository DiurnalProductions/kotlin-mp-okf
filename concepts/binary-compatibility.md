---
type: Concept
title: Binary Compatibility
description: ABI stability concerns when publishing or consuming KMP libraries across versions and targets.
tags: [kotlin-multiplatform, binary-compatibility, publishing, libraries]
prerequisites:
  - concepts/shared-dependencies
  - concepts/transitive-constraints
related:
  - concepts/portability-constraints
  - concepts/shared-module
resource: "https://kotlinlang.org/docs/multiplatform-publish-lib.html"
timestamp: 2026-01-01
---

## Summary

Binary compatibility in KMP concerns whether consumers compiled against one version of a shared library can link against another without recompilation. Published KMP artifacts include per-target binaries plus common metadata. Breaking changes to public APIs in `commonMain` affect all consumers on all targets.

## Mental model

KMP library publication produces a **matrix of artifacts**: metadata + JVM jar + Native klib + JS IR module. Consumers depend on the common ABI contract. Unlike JVM-only libraries, you must consider:

- Inline function changes (break Native/JS binaries).
- Sealed class hierarchy changes.
- Expect/actual additions (consumers must provide new actuals).
- `@Experimental` API promotion and removal.

Semantic versioning applies to the common API. Target-specific actuals are internal to the library unless exposed.

## Example

```kotlin
// Breaking — removing public API
// class UserRepository { fun load() }  // removed in 2.0

// Safer — deprecate, then remove
@Deprecated("Use loadUser()", ReplaceWith("loadUser()"))
fun load() = loadUser()
```

For published libraries, run compatibility validators and avoid breaking `commonMain` public API without major version bumps.

## Common mistakes

- Publishing internal expect/actual as public API surface.
- Inline function signature changes in patch releases.
- Not testing consumer projects on all targets after API changes.
- Exposing platform types in public common API (locks ABI to platform-specific semantics).

## Related concepts

- [Portability Constraints](portability-constraints.md)
- [Shared Module](shared-module.md)
