---
id: kmp.shared_dtos
type: concept
title: Shared DTOs
description: Data transfer objects defined once in commonMain for API payloads, persistence records, and cross-layer communication.
tags: [kotlin-multiplatform, dto, models, serialization]
prerequisites:
  - kmp.kotlinx_serialization
related:
  - kmp.platform_safe_models
  - kmp.shared_domain_layer
  - kmp.networking_abstraction
resource: https://kotlinlang.org/docs/multiplatform-share-libraries.html
timestamp: 2026-01-01
---

## Summary

Shared DTOs are immutable (or carefully mutable) data classes in commonMain representing wire formats and storage schemas. They are separate from domain entities when using clean architecture, or merged in simpler projects. DTOs use `kotlinx.serialization` annotations and portable types only.

## Mental model

DTOs are **boundary artifacts** — they describe data as it crosses system edges (network, database, cache). Map DTOs to domain models in the data layer, not in use cases.

```
API JSON → UserDto → mapper → User (domain entity)
```

Keep DTOs stable for backward compatibility. Prefer additive changes (optional fields) over renames without migration. Use `@SerialName` when wire names differ from Kotlin properties.

## Example

```kotlin
@Serializable
data class ProductDto(
    val id: String,
    @SerialName("display_name") val displayName: String,
    val priceCents: Long,
    val tags: List<String> = emptyList()
)

fun ProductDto.toDomain(): Product = Product(
    id = id,
    name = displayName,
    price = Money.cents(priceCents),
    tags = tags
)
```

## Common mistakes

- Using platform types (`UUID`, `Date`, `BigDecimal`) in DTOs.
- Leaking DTOs into UI layer without mapping to view models.
- Breaking wire compatibility by renaming properties without `@SerialName`.
- Mixing domain validation logic into DTO data classes.

## Related concepts

- [Platform-Safe Models](platform-safe-models.md)
- [Shared Domain Layer](shared-domain-layer.md)
- [Networking Abstraction](networking-abstraction.md)
