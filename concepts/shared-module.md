---
type: Concept
title: Shared Module
description: The KMP library module containing commonMain and platform source sets that hold portable business logic consumed by all app targets.
tags: [kotlin-multiplatform, modules, shared-code, architecture]
prerequisites:
  - concepts/source-sets
related:
  - concepts/platform-modules
  - concepts/shared-domain-layer
  - concepts/gradle-configuration
resource: "https://kotlinlang.org/docs/multiplatform-discover-project.html"
timestamp: 2026-01-01
---

## Summary

The shared module (often named `shared`, `core`, or `domain`) is a Kotlin Multiplatform library that compiles for all targets. It contains `commonMain` with domain logic, interfaces, and models, plus platform source sets for actual implementations. Application modules depend on the shared module; the shared module never depends on application modules.

## Mental model

The shared module is the **center of gravity** in a KMP project. Everything that can be expressed portably lives here. App modules are thin shells: they wire DI, provide platform implementations, and host UI.

Typical module graph:

```
:androidApp  ──► :shared
:iosApp      ──► :shared (as framework)
:desktopApp  ──► :shared
```

A well-structured shared module has no UI framework dependencies, no application entry points, and minimal expect/actual surface. It exports APIs that apps consume.

## Example

```
shared/
  src/
    commonMain/kotlin/
      domain/
      data/
      di/
    androidMain/kotlin/
      platform/
    iosMain/kotlin/
      platform/
  build.gradle.kts  // kotlin { multiplatform { ... } }
```

## Common mistakes

- Naming it `app` and mixing UI with shared logic.
- Depending on `:androidApp` from `:shared` (inverted dependency).
- Publishing the shared module without considering binary compatibility for consumers.
- Monolithic shared module that should be split into `:domain`, `:data`, `:core` as project grows.

## Related concepts

- [Platform Modules](platform-modules.md)
- [Shared Domain Layer](shared-domain-layer.md)
- [Gradle Configuration](gradle-configuration.md)
