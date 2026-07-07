---
type: Concept
title: kotlinx.serialization
description: Multiplatform serialization framework for encoding and decoding structured data in shared KMP code.
tags: [kotlin-multiplatform, serialization, kotlinx-serialization, json]
prerequisites:
  - concepts/portability-constraints
  - concepts/shared-module
related:
  - concepts/shared-dtos
  - concepts/serialization-formats
  - concepts/platform-safe-models
resource: "https://github.com/Kotlin/kotlinx.serialization"
timestamp: 2026-01-01
---

## Summary

`kotlinx.serialization` provides compile-time code generation for serializers that work across KMP targets. It supports JSON, ProtoBuf, CBOR, and other formats via format-specific modules. It is the standard choice for serializing shared DTOs in commonMain without reflection-based frameworks.

## Mental model

Serialization in KMP must be **compile-time and portable**. `kotlinx.serialization` generates `KSerializer` instances at compile time — no reflection on Native/JS. The pattern:

```
@Serializable data class → generated serializer → Json.encodeToString / decodeFromString
```

Apply the plugin in the shared module. Declare `kotlin("plugin.serialization")` and add format dependencies (`kotlinx-serialization-json`) to commonMain.

## Example

```kotlin
@Serializable
data class UserDto(val id: String, val email: String, val createdAt: String)

// commonMain
val json = Json { ignoreUnknownKeys = true; isLenient = true }
fun parseUser(raw: String): UserDto = json.decodeFromString(raw)
```

## Common mistakes

- Using Gson, Moshi, or Jackson in commonMain (JVM-only, reflection-based).
- Forgetting `@Serializable` on nested types.
- Polymorphic serialization without sealed class discipline (security and compatibility risks).
- Different `Json` configuration per platform (breaks deterministic parsing in shared code).

## Related concepts

- [Shared DTOs](shared-dtos.md)
- [Serialization Formats](serialization-formats.md)
- [Platform-Safe Models](platform-safe-models.md)
