---
type: Concept
title: Portability Constraints
description: "Language, standard library, and third-party library limitations that restrict what can live in shared KMP code."
tags: [kotlin-multiplatform, portability, constraints, libraries]
prerequisites:
  - concepts/abstraction-boundaries
related:
  - concepts/shared-dependencies
  - concepts/platform-safe-models
  - concepts/binary-compatibility
resource: "https://kotlinlang.org/docs/multiplatform-share-libraries.html"
timestamp: 2026-01-01
---

## Summary

Portability constraints define what Kotlin and library features are safe across all declared targets. Shared code must use only multiplatform-compatible APIs. Reflection, certain concurrency primitives, file I/O, and many JVM-only libraries are unavailable or behave differently on Native and JS targets.

## Mental model

Shared code runs through the **lowest common denominator** of your target set. Adding Native or JS targets tightens constraints dramatically compared to JVM-only sharing. Every dependency added to `commonMain` must publish artifacts for every target.

Constraint categories:

- **Language**: `expect`/`actual`, limited reflection on Native/JS.
- **stdlib**: `kotlinx` multiplatform libraries vs JDK-only APIs.
- **Third-party**: Must be KMP-published with metadata for all targets.
- **Runtime**: GC vs manual memory, single-threaded JS, etc.

Design shared code as if it must compile for the strictest target you support, not the most permissive.

## Example

```kotlin
// NOT portable — java.util.Date, java.io.File
import java.io.File  // JVM-only in practice for file paths

// Portable — kotlinx-datetime, multiplatform file APIs via abstraction
import kotlinx.datetime.Clock
import kotlinx.datetime.Instant

data class Event(val occurredAt: Instant)  // works on all targets
```

## Common mistakes

- Using `java.*` or `javax.*` packages in commonMain.
- Assuming `System.currentTimeMillis()` exists on all targets.
- Adding a JVM-only library to common dependencies and only discovering failure at iOS compile time.
- Using `ThreadLocal`, `synchronized`, or JVM-specific concurrency without multiplatform alternatives.

## Related concepts

- [Shared Dependencies](shared-dependencies.md)
- [Platform-Safe Models](platform-safe-models.md)
- [Binary Compatibility](binary-compatibility.md)
