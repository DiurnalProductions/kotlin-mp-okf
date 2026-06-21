---
id: kmp.repository_abstraction
type: concept
title: Repository Abstraction
description: Defining data access interfaces in shared code with platform or multiplatform implementations behind the boundary.
tags: [kotlin-multiplatform, repository, data-layer, abstraction]
prerequisites:
  - kmp.shared_domain_layer
  - kmp.abstraction_boundaries
related:
  - kmp.dependency_inversion
  - kmp.networking_abstraction
  - kmp.filesystem_abstraction
resource: https://kotlinlang.org/docs/multiplatform-connect-to-apis.html
timestamp: 2026-01-01
---

## Summary

Repository interfaces live in shared code (domain or data contract package). Implementations fetch from network, database, or cache using platform or multiplatform libraries. Use cases depend on interfaces, not implementations. This is the primary pattern for data access in KMP.

## Mental model

```
UseCase → Repository (interface, commonMain)
              ↑
    ┌─────────┼─────────┐
    │         │         │
 ApiRepo   SqlDelight  FakeRepo
 (Ktor)    (multiplatform DB)
```

Implementations may live in `commonMain` when using multiplatform libraries (SQLDelight, DataStore multiplatform). Platform-specific storage (Keychain, Room) gets platform implementations wired at the app edge.

## Example

```kotlin
// commonMain
interface UserRepository {
    suspend fun getUser(id: String): User?
    suspend fun saveUser(user: User)
    fun observeUser(id: String): Flow<User?>
}

class GetUserUseCase(private val repo: UserRepository) {
    suspend operator fun invoke(id: String) = repo.getUser(id)
}
```

## Common mistakes

- Concrete repository classes in domain package without interface.
- Repository returning DTOs to use cases without mapping to domain.
- Single god repository for all data concerns.
- expect/actual repositories instead of interface + DI.

## Related concepts

- [Dependency Inversion Across Platforms](dependency-inversion.md)
- [Networking Abstraction](networking-abstraction.md)
- [Filesystem Abstraction](filesystem-abstraction.md)
