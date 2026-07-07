---
type: Concept
title: Networking Abstraction
description: "HTTP clients, TLS, certificate pinning, and transport configuration isolated per platform behind shared interfaces."
tags: [kotlin-multiplatform, networking, http, ktor, abstraction]
prerequisites:
  - concepts/abstraction-boundaries
related:
  - concepts/repository-abstraction
  - concepts/platform-dependencies
  - concepts/shared-dtos
resource: "https://ktor.io/docs/client-multiplatform.html"
timestamp: 2026-01-01
---

## Summary

Networking in KMP typically uses Ktor Client with a multiplatform core and platform-specific engines (OkHttp, Darwin, CIO, JS). Shared code defines API clients, request/response models, and retry logic. Platform source sets provide engine configuration, certificate pinning, and proxy settings.

## Mental model

Separate **protocol logic** (shared) from **transport configuration** (platform):

```
commonMain: ApiService, DTOs, auth token interceptor logic
androidMain: OkHttp engine + network security config
iosMain: Darwin engine + ATS settings
```

Shared networking code should be deterministic and testable with a fake engine or mock client. Never import engine-specific types in commonMain.

## Example

```kotlin
// commonMain
class UserApi(private val client: HttpClient) {
    suspend fun fetchUser(id: String): User =
        client.get("https://api.example.com/users/$id").body()
}

// androidMain — factory
fun createHttpClient(): HttpClient = HttpClient(OkHttp) { /* ... */ }

// iosMain
fun createHttpClient(): HttpClient = HttpClient(Darwin) { /* ... */ }
```

## Common mistakes

- Hardcoding platform TLS behavior in shared code.
- Using JVM-only HTTP libraries (Retrofit, OkHttp directly) in commonMain.
- Leaking `HttpClient` configuration details into domain layer.
- Ignoring offline/cache behavior differences on mobile vs web.

## Related concepts

- [Repository Abstraction](repository-abstraction.md)
- [Platform Dependencies](platform-dependencies.md)
- [Shared DTOs](shared-dtos.md)
