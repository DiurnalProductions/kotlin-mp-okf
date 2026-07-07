---
okf_version: "0.1"
id: kotlin-mp-okf
name: Kotlin Multiplatform Knowledge Pack
version: "0.1"
description: "OKF knowledge base for Kotlin Multiplatform (shared code, expect/actual, platform abstraction, architecture, and concurrency)"
tags: [kotlin, multiplatform, kmp, shared-code, architecture]
---

# Kotlin Multiplatform Knowledge Pack

> Kotlin Multiplatform is a system for sharing logic across platforms while preserving platform-native implementations where necessary.

This pack is a plug-and-play OKF knowledge base for Kotlin Multiplatform engineering. It emphasizes shared code architecture, the expect/actual system, platform abstraction, dependency boundaries, concurrency behavior, and real-world multi-target design. It assumes separate Kotlin, Compose, and platform-specific packs exist and may reference them.

## Start Here

Begin with the core mental model: understand what belongs in shared code versus platform code, which compilation targets exist, and where abstraction boundaries must be drawn. KMP is not about sharing UI or duplicating platform APIs in common code — it is about isolating portable business logic behind deliberate boundaries.

**Recommended learning progression:**

1. [Shared vs Platform Code](concepts/shared-vs-platform.md)
2. [Compilation Targets](concepts/compilation-targets.md)
3. [Abstraction Boundaries](concepts/abstraction-boundaries.md)
4. [Source Sets](concepts/source-sets.md)
5. [Shared Module](concepts/shared-module.md)
6. [Expect Declarations](concepts/expect-declarations.md)
7. [Actual Implementations](concepts/actual-implementations.md)
8. [Repository Abstraction](concepts/repository-abstraction.md)
9. [Coroutines Across Platforms](concepts/coroutines-multiplatform.md)
10. [Clean Architecture in KMP](concepts/clean-architecture-kmp.md)

---

## Core Mental Model

* [Shared vs Platform Code](concepts/shared-vs-platform.md) — The foundational split between portable logic and platform-specific implementations.
* [Compilation Targets](concepts/compilation-targets.md) — JVM, iOS (Native), JS, and other targets and what each implies.
* [Abstraction Boundaries](concepts/abstraction-boundaries.md) — Where to draw lines so shared code stays platform-agnostic.
* [Portability Constraints](concepts/portability-constraints.md) — Language, library, and runtime limits that shape shared code design.

## Project Structure

* [Source Sets](concepts/source-sets.md) — How Gradle organizes code by target and dependency hierarchy.
* [Shared Module](concepts/shared-module.md) — The common module that holds portable business logic.
* [Platform Modules](concepts/platform-modules.md) — Android, iOS, JVM, and JS entry modules that consume shared code.
* [Hierarchical Source Sets](concepts/hierarchical-source-sets.md) — commonMain, androidMain, iosMain, jsMain and their relationships.

## Expect/Actual System

* [Expect Declarations](concepts/expect-declarations.md) — Declaring platform contracts in shared code.
* [Actual Implementations](concepts/actual-implementations.md) — Supplying per-target implementations of expect declarations.
* [Expect/Actual Resolution Rules](concepts/expect-actual-resolution.md) — How the compiler matches expects to actuals.
* [Platform-Specific Overrides](concepts/platform-specific-overrides.md) — Targeting individual platforms within the expect/actual mechanism.
* [Expect/Actual Architecture](concepts/expect-actual-architecture.md) — When and how to use expect/actual without making it your primary design tool.

## Platform Abstraction

* [Filesystem Abstraction](concepts/filesystem-abstraction.md) — Isolating file I/O behind shared interfaces.
* [Networking Abstraction](concepts/networking-abstraction.md) — HTTP clients, TLS, and transport concerns per platform.
* [Threading Differences](concepts/threading-differences.md) — How threads, main loops, and schedulers differ across targets.
* [Time APIs](concepts/time-apis.md) — Clocks, time zones, and duration handling in shared code.
* [Device Capabilities](concepts/device-capabilities.md) — Sensors, permissions, and hardware access patterns.

## Dependency Management

* [Gradle Configuration](concepts/gradle-configuration.md) — Conceptual model of KMP Gradle setup.
* [Shared Dependencies](concepts/shared-dependencies.md) — Libraries safe to declare in common source sets.
* [Platform Dependencies](concepts/platform-dependencies.md) — Target-specific libraries and their isolation.
* [Transitive Constraints](concepts/transitive-constraints.md) — How dependency graphs propagate across targets.
* [Binary Compatibility](concepts/binary-compatibility.md) — ABI stability concerns for published KMP libraries.

## Concurrency Model

* [Coroutines Across Platforms](concepts/coroutines-multiplatform.md) — kotlinx.coroutines as the shared concurrency primitive.
* [Dispatchers Differences](concepts/dispatchers-differences.md) — Main, IO, and Default dispatchers per platform.
* [Platform Threading Model](concepts/platform-threading-model.md) — JVM threads, Native workers, JS single-threaded execution.
* [Structured Concurrency](concepts/structured-concurrency.md) — Scopes, cancellation, and consistency across targets.

## Serialization

* [kotlinx.serialization](concepts/kotlinx-serialization.md) — Multiplatform serialization framework fundamentals.
* [Shared DTOs](concepts/shared-dtos.md) — Data transfer objects defined once in common code.
* [Platform-Safe Models](concepts/platform-safe-models.md) — Types and annotations that compile on every target.
* [Serialization Formats](concepts/serialization-formats.md) — JSON, ProtoBuf, and binary format trade-offs.

## Architecture Patterns

* [Shared Domain Layer](concepts/shared-domain-layer.md) — Entities, use cases, and business rules in common code.
* [Repository Abstraction](concepts/repository-abstraction.md) — Interface in shared code, implementation per platform.
* [Clean Architecture in KMP](concepts/clean-architecture-kmp.md) — Layering domain, data, and presentation across targets.
* [Dependency Inversion Across Platforms](concepts/dependency-inversion.md) — Injecting platform implementations into shared logic.

## UI Architecture

* [Shared Logic vs Platform UI](concepts/shared-logic-vs-ui.md) — Keeping presentation out of shared modules.
* [Compose Multiplatform Overview](concepts/compose-multiplatform-overview.md) — Conceptual separation of shared UI from platform shells.
* [UI Leakage Prevention](concepts/ui-leakage-prevention.md) — Patterns that stop UI frameworks from entering common code.

## Testing Strategy

* [Shared Tests](concepts/shared-tests.md) — commonTest for logic that runs on all targets.
* [Platform-Specific Tests](concepts/platform-specific-tests.md) — androidTest, iosTest, and target-only verification.
* [Mocking Expectations](concepts/mocking-expectations.md) — Fakes and test doubles across the abstraction boundary.
* [Test Source Sets](concepts/test-source-sets.md) — commonTest, platform test sets, and dependency wiring.

## Performance Considerations

* [Native Compilation Costs](concepts/native-compilation-costs.md) — Build time and tooling overhead for Kotlin/Native.
* [JVM vs Native Runtime](concepts/jvm-vs-native-runtime.md) — GC, JIT, and execution model differences.
* [Memory Model Differences](concepts/memory-model-differences.md) — Shared mutable state rules across targets.
* [Startup Time Considerations](concepts/startup-time.md) — Cold start, framework init, and binary size trade-offs.
