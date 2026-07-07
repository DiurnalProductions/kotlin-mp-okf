---
type: Concept
title: Serialization Formats
description: "JSON, ProtoBuf, CBOR, and binary format trade-offs for multiplatform data exchange."
tags: [kotlin-multiplatform, serialization, json, protobuf, binary]
prerequisites:
  - concepts/kotlinx-serialization
related:
  - concepts/shared-dtos
  - concepts/networking-abstraction
resource: "https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/formats.md"
timestamp: 2026-01-01
---

## Summary

`kotlinx.serialization` supports multiple formats. JSON is human-readable and web-friendly. ProtoBuf and CBOR are compact binary formats for bandwidth-sensitive or storage-efficient scenarios. Format choice affects schema evolution, debugging, and cross-platform interoperability with non-Kotlin systems.

## Mental model

Choose format based on **consumer and constraint**:

| Format | Size | Human-readable | Schema evolution |
|--------|------|----------------|------------------|
| JSON | Large | Yes | Flexible with unknown keys |
| ProtoBuf | Small | No | Requires field numbers discipline |
| CBOR | Small | No | Similar to JSON binary |

Configure format once in shared code. All platforms must use identical `Json { }` or `ProtoBuf` settings for deterministic behavior.

## Example

```kotlin
// JSON — typical API
val json = Json {
    ignoreUnknownKeys = true
    encodeDefaults = true
}

// ProtoBuf — compact storage
val proto = ProtoBuf
val bytes: ByteArray = proto.encodeToByteArray(ProductDto.serializer(), product)
```

## Common mistakes

- Different JSON settings on Android vs iOS (subtle parsing bugs).
- Using ProtoBuf without stable field numbers across versions.
- Serializing platform-specific types that lack portable serializers.
- Mixing JSON and binary for the same API surface without content negotiation.

## Related concepts

- [Shared DTOs](shared-dtos.md)
- [Networking Abstraction](networking-abstraction.md)
