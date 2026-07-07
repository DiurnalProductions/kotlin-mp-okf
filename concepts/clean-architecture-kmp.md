---
type: Concept
title: Clean Architecture in KMP
description: "Layering domain, data, and presentation in KMP with strict dependency direction and platform implementations at the outer edge."
tags: [kotlin-multiplatform, clean-architecture, layers, architecture]
prerequisites:
  - concepts/shared-domain-layer
  - concepts/repository-abstraction
related:
  - concepts/dependency-inversion
  - concepts/shared-logic-vs-ui
  - concepts/ui-leakage-prevention
resource: "https://kotlinlang.org/docs/multiplatform-discover-project.html"
timestamp: 2026-01-01
---

## Summary

Clean architecture in KMP maps layers to source sets and modules. Domain and use cases sit in inner `commonMain` layers. Data implementations may be common (multiplatform DB/HTTP) or platform-specific. Presentation logic (ViewModels) can be shared; UI is outermost on each platform. Dependencies point inward only.

## Mental model

```
        ┌─────────────────────────────────────┐
        │ Platform UI (outer)                 │
        ├─────────────────────────────────────┤
        │ Presentation (shared ViewModels)    │
        ├─────────────────────────────────────┤
        │ Domain (entities, use cases)        │
        ├─────────────────────────────────────┤
        │ Data interfaces (repos)             │
        ├─────────────────────────────────────┤
        │ Data implementations / platform I/O │
        └─────────────────────────────────────┘
              dependencies flow inward ↑
```

KMP strengthens clean architecture because platform I/O is naturally pushed to the outer ring. The shared module is the inner hexagon; apps are adapters.

## Example

```
shared/commonMain
  domain/         PlaceOrderUseCase, Order
  data/api/       OrderRepository interface, OrderDto, OrderMapper
  data/impl/      OrderRepositoryImpl (Ktor + SQLDelight)
  presentation/   OrderViewModel

androidApp/       Compose screens, Activity, DI module
iosApp/           SwiftUI screens, DI wiring
```

## Common mistakes

- Skipping domain layer and putting logic in ViewModels.
- Data layer importing presentation types.
- Circular module dependencies between :shared and :features.
- Clean architecture ceremony without testable use cases (layers as folders only).

## Related concepts

- [Dependency Inversion Across Platforms](dependency-inversion.md)
- [Shared Logic vs Platform UI](shared-logic-vs-ui.md)
- [UI Leakage Prevention](ui-leakage-prevention.md)
