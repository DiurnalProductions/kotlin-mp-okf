---
type: Concept
title: Abstraction Boundaries
description: Deliberate interfaces and module edges that isolate platform concerns from portable shared logic.
tags: [kotlin-multiplatform, architecture, abstraction, boundaries]
prerequisites:
  - concepts/shared-vs-platform
  - concepts/compilation-targets
related:
  - concepts/repository-abstraction
  - concepts/expect-declarations
  - concepts/filesystem-abstraction
resource: "https://kotlinlang.org/docs/multiplatform-connect-to-apis.html"
timestamp: 2026-01-01
---

## Summary

Abstraction boundaries are the intentional seams where shared code stops and platform code begins. They are expressed as interfaces, expect declarations, or dependency-injected implementations. Well-placed boundaries keep shared modules free of platform imports while allowing each target to supply its own behavior.

## Mental model

Draw boundaries at **capability edges**, not at file-system convenience. Ask: "What does my business logic need from the platform?" — not "What API does the platform offer?" Boundaries should be narrow, stable, and testable.

```
shared logic ──► PlatformService (interface) ◄── android impl
                                              ◄── ios impl
                                              ◄── jvm impl
```

The shared layer depends only on the interface. Platform modules depend on both the interface (defined in shared) and platform SDKs. This is dependency inversion applied across compilation targets.

Prefer **interface + factory/DI** over expect/actual when multiple implementations or test doubles are needed. Reserve expect/actual for low-level primitives (thread identity, default dispatchers) where a single canonical implementation per target is sufficient.

## Example

```kotlin
// commonMain
interface SecureStorage {
    suspend fun put(key: String, value: String)
    suspend fun get(key: String): String?
}

class AuthRepository(private val storage: SecureStorage) {
    suspend fun saveToken(token: String) = storage.put("token", token)
}

// androidMain
class AndroidSecureStorage(context: Context) : SecureStorage { /* EncryptedSharedPreferences */ }

// iosMain
class IosSecureStorage : SecureStorage { /* Keychain */ }
```

## Common mistakes

- Leaking platform types through abstraction interfaces (returning `Bitmap` or `NSData` from shared interfaces).
- Creating "god interfaces" that bundle unrelated platform capabilities.
- Using expect/actual for every platform difference instead of designing proper abstractions.
- Placing abstractions in platform modules instead of shared modules (inverting the dependency direction).

## Related concepts

- [Repository Abstraction](repository-abstraction.md)
- [Expect Declarations](expect-declarations.md)
- [Filesystem Abstraction](filesystem-abstraction.md)
