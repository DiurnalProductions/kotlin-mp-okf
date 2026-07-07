---
type: Concept
title: Platform-Safe Models
description: "Types, annotations, and patterns for domain and data models that compile and behave deterministically on every KMP target."
tags: [kotlin-multiplatform, models, types, portability]
prerequisites:
  - concepts/shared-dtos
  - concepts/portability-constraints
related:
  - concepts/kotlinx-serialization
  - concepts/time-apis
resource: "https://kotlinlang.org/docs/multiplatform-share-libraries.html"
timestamp: 2026-01-01
---

## Summary

Platform-safe models use only Kotlin multiplatform types: primitives, `String`, collections, `kotlinx.datetime` types, and other portable libraries. They avoid JVM numeric types with platform-specific behavior, platform enums, and reference types that do not exist on all targets.

## Mental model

A platform-safe model passes the **"compile on iOS" test** — if it compiles in `commonMain` for all declared targets without expect/actual, it is structurally safe. Semantic safety additionally requires deterministic equality, hashing, and serialization.

Safe choices:

- `Long` for money in minor units (not `BigDecimal` in common without library)
- `Instant` from kotlinx-datetime (not `java.time.Instant`)
- `List`, `Map`, `Set` (not Java collections)
- Sealed classes and enums for closed hierarchies

## Example

```kotlin
@Serializable
data class Order(
    val id: String,
    val placedAt: Instant,
    val totalCents: Long,
    val status: OrderStatus
)

@Serializable
enum class OrderStatus { PENDING, CONFIRMED, SHIPPED, CANCELLED }
```

## Common mistakes

- `java.math.BigDecimal` in common models.
- `UUID` type without multiplatform alternative (use `String`).
- Non-deterministic `toString()` or equality depending on platform locale.
- Using `Throwable` stack traces or platform exception types in domain models.

## Related concepts

- [kotlinx.serialization](kotlinx-serialization.md)
- [Time APIs](time-apis.md)
