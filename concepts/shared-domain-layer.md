---
type: Concept
title: Shared Domain Layer
description: "Entities, value objects, use cases, and business rules expressed once in commonMain independent of platform or UI."
tags: [kotlin-multiplatform, domain, clean-architecture, use-cases]
prerequisites:
  - concepts/abstraction-boundaries
  - concepts/shared-module
related:
  - concepts/repository-abstraction
  - concepts/clean-architecture-kmp
  - concepts/shared-dtos
resource: "https://kotlinlang.org/docs/multiplatform-discover-project.html"
timestamp: 2026-01-01
---

## Summary

The shared domain layer contains pure business logic: entities, value objects, domain services, and use cases. It has no dependencies on frameworks, platforms, or I/O. It defines repository interfaces that the data layer implements per platform or via multiplatform libraries.

## Mental model

Domain layer invariants:

- No imports from `android.*`, `platform.*`, UI, or HTTP client libraries.
- No `suspend` network calls inside entities (belongs in use cases/repos).
- Express business rules as testable pure functions and use case classes.

```
Domain (commonMain)
  ├── Entity: Order, User, Money
  ├── UseCase: PlaceOrder, ValidateEmail
  └── Interface: OrderRepository, PaymentGateway
```

Domain is the **highest-value shared code** — it encodes what the product does, independent of how data is fetched or displayed.

## Example

```kotlin
// commonMain
data class Money(val cents: Long) {
    operator fun plus(other: Money) = Money(cents + other.cents)
}

class PlaceOrderUseCase(private val orders: OrderRepository) {
    suspend fun execute(cart: Cart): Result<Order> {
        if (cart.isEmpty()) return Result.failure(DomainError.EmptyCart)
        return orders.submit(cart.toOrder())
    }
}
```

## Common mistakes

- Anemic domain models with all logic in ViewModels.
- Domain entities with serialization annotations tied to wire format (prefer separate DTOs).
- Repository interfaces that expose platform types.
- Use cases that import Ktor or SQL libraries directly.

## Related concepts

- [Repository Abstraction](repository-abstraction.md)
- [Clean Architecture in KMP](clean-architecture-kmp.md)
- [Shared DTOs](shared-dtos.md)
