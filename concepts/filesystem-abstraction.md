---
id: kmp.filesystem_abstraction
type: concept
title: Filesystem Abstraction
description: Isolating file and path operations behind shared interfaces so common code avoids platform-specific I/O APIs.
tags: [kotlin-multiplatform, filesystem, abstraction, io]
prerequisites:
  - kmp.abstraction_boundaries
related:
  - kmp.repository_abstraction
  - kmp.device_capabilities
resource: https://kotlinlang.org/docs/multiplatform-connect-to-apis.html
timestamp: 2026-01-01
---

## Summary

Filesystem access differs radically across JVM, Native, and JS. Shared code must not use `java.io.File` or platform path APIs directly. Instead, define abstractions for reading, writing, and resolving paths, with platform implementations handling sandbox rules, content URIs, and app container directories.

## Mental model

Filesystem abstraction treats storage as a **capability service**, not a path string. Shared code requests "cache directory for user data" or "read bytes from logical key" without knowing whether the backing store is `/data/data/...`, `NSDocumentDirectory`, or IndexedDB.

Layers:

```
commonMain: interface FileStorage { suspend fun read(key): ByteArray }
androidMain: Context.filesDir / content resolver
iosMain: FileManager.default.urls
jsMain: localStorage or in-memory fallback
```

Consider multiplatform libraries like Okio (with platform backends) or `kotlinx-io` when appropriate, but still hide them behind domain-facing interfaces.

## Example

```kotlin
// commonMain
interface AppFileStore {
    suspend fun writeText(name: String, content: String)
    suspend fun readText(name: String): String?
}

class SettingsCache(private val files: AppFileStore) {
    suspend fun save(json: String) = files.writeText("settings.json", json)
}
```

## Common mistakes

- Using absolute paths in shared code.
- Assuming a writable filesystem exists (JS, some iOS contexts differ).
- Exposing `File` or `Path` types from shared interfaces.
- Not handling Android scoped storage and content URI indirection in the platform impl.

## Related concepts

- [Repository Abstraction](repository-abstraction.md)
- [Device Capabilities](device-capabilities.md)
