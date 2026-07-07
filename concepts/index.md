# Concepts

* [Abstraction Boundaries](abstraction-boundaries.md) - Deliberate interfaces and module edges that isolate platform concerns from portable shared logic.
* [Actual Implementations](actual-implementations.md) - Platform-specific implementations that fulfill expect declarations declared in shared common code.
* [Binary Compatibility](binary-compatibility.md) - ABI stability concerns when publishing or consuming KMP libraries across versions and targets.
* [Clean Architecture in KMP](clean-architecture-kmp.md) - Layering domain, data, and presentation in KMP with strict dependency direction and platform implementations at the outer edge.
* [Compilation Targets](compilation-targets.md) - The distinct backends KMP compiles to — JVM, Native (iOS/macOS), JS/Wasm — and the runtime characteristics each imposes.
* [Compose Multiplatform Overview](compose-multiplatform-overview.md) - Conceptual overview of sharing UI with Compose Multiplatform while keeping platform shells and integrations separate.
* [Coroutines Across Platforms](coroutines-multiplatform.md) - Using kotlinx.coroutines as the shared concurrency primitive that compiles and behaves consistently across KMP targets.
* [Dependency Inversion Across Platforms](dependency-inversion.md) - Injecting platform implementations into shared logic at application composition roots using DI frameworks or manual wiring.
* [Device Capabilities](device-capabilities.md) - Accessing sensors, permissions, biometrics, and hardware features through abstractions rather than direct platform API usage in shared code.
* [Dispatchers Differences](dispatchers-differences.md) - How Dispatchers.Main, Dispatchers.IO, and Dispatchers.Default map to platform-specific thread pools and executors.
* [Expect/Actual Architecture](expect-actual-architecture.md) - Architectural guidance for when expect/actual is appropriate versus interfaces, and how to avoid making it the center of your design.
* [Expect/Actual Resolution Rules](expect-actual-resolution.md) - Compiler rules governing how expect declarations in common code are matched to actual implementations per target.
* [Expect Declarations](expect-declarations.md) - Declaring platform-dependent contracts in shared code that each target must fulfill with an actual implementation.
* [Filesystem Abstraction](filesystem-abstraction.md) - Isolating file and path operations behind shared interfaces so common code avoids platform-specific I/O APIs.
* [Gradle Configuration](gradle-configuration.md) - Conceptual model of KMP Gradle plugin setup including targets, source sets, dependencies, and publication.
* [Hierarchical Source Sets](hierarchical-source-sets.md) - The commonMain, androidMain, iosMain, jsMain hierarchy and intermediate sets like appleMain and nativeMain that reduce duplication across related targets.
* [JVM vs Native Runtime](jvm-vs-native-runtime.md) - Execution model differences between JVM/ART and Kotlin/Native LLVM binaries affecting performance and design.
* [kotlinx.serialization](kotlinx-serialization.md) - Multiplatform serialization framework for encoding and decoding structured data in shared KMP code.
* [Memory Model Differences](memory-model-differences.md) - Shared mutable state, object freezing, and concurrency rules that differ across JVM, Native, and JS KMP targets.
* [Mocking Expectations](mocking-expectations.md) - Fakes, test doubles, and stub strategies for abstractions and expect declarations in KMP tests.
* [Native Compilation Costs](native-compilation-costs.md) - Build time, tooling overhead, and developer workflow impact of Kotlin/Native targets in KMP projects.
* [Networking Abstraction](networking-abstraction.md) - HTTP clients, TLS, certificate pinning, and transport configuration isolated per platform behind shared interfaces.
* [Platform Dependencies](platform-dependencies.md) - Target-specific libraries declared in platform source sets that must never leak into commonMain shared code.
* [Platform Modules](platform-modules.md) - Target-specific application or library modules that consume the shared KMP module and host UI, DI wiring, and platform integrations.
* [Platform-Safe Models](platform-safe-models.md) - Types, annotations, and patterns for domain and data models that compile and behave deterministically on every KMP target.
* [Platform-Specific Overrides](platform-specific-overrides.md) - Targeting individual platforms or platform families within the expect/actual and source set hierarchy.
* [Platform-Specific Tests](platform-specific-tests.md) - androidTest, iosTest, and target-specific tests for actual implementations, integrations, and platform SDK behavior.
* [Platform Threading Model](platform-threading-model.md) - Deep comparison of JVM threads, Kotlin/Native workers, and JavaScript single-threaded execution as they affect KMP design.
* [Portability Constraints](portability-constraints.md) - Language, standard library, and third-party library limitations that restrict what can live in shared KMP code.
* [Repository Abstraction](repository-abstraction.md) - Defining data access interfaces in shared code with platform or multiplatform implementations behind the boundary.
* [Serialization Formats](serialization-formats.md) - JSON, ProtoBuf, CBOR, and binary format trade-offs for multiplatform data exchange.
* [Shared Dependencies](shared-dependencies.md) - Multiplatform libraries declared in commonMain that must compile and link for every declared target.
* [Shared Domain Layer](shared-domain-layer.md) - Entities, value objects, use cases, and business rules expressed once in commonMain independent of platform or UI.
* [Shared DTOs](shared-dtos.md) - Data transfer objects defined once in commonMain for API payloads, persistence records, and cross-layer communication.
* [Shared Logic vs Platform UI](shared-logic-vs-ui.md) - Separating presentation from portable business logic so shared modules remain UI-framework agnostic.
* [Shared Module](shared-module.md) - The KMP library module containing commonMain and platform source sets that hold portable business logic consumed by all app targets.
* [Shared Tests](shared-tests.md) - Unit and integration tests in commonTest that verify portable logic runs correctly on all supported targets.
* [Shared vs Platform Code](shared-vs-platform.md) - The foundational split between portable business logic in common source sets and platform-specific implementations in target source sets.
* [Source Sets](source-sets.md) - Gradle source set groupings that map Kotlin files to compilation targets and define dependency relationships between code layers.
* [Startup Time Considerations](startup-time.md) - Cold start, framework initialization, binary size, and lazy loading trade-offs across KMP targets.
* [Structured Concurrency](structured-concurrency.md) - Scopes, parent-child job relationships, and cancellation propagation consistent across KMP targets.
* [Test Source Sets](test-source-sets.md) - commonTest, androidUnitTest, iosTest, and hierarchical test source set wiring for KMP projects.
* [Threading Differences](threading-differences.md) - How threads, main loops, and execution models differ across JVM, Native, and JS KMP targets.
* [Time APIs](time-apis.md) - Clocks, instants, durations, and time zones in shared KMP code using multiplatform-safe libraries.
* [Transitive Constraints](transitive-constraints.md) - How dependency graphs propagate through KMP source sets and the impact on binary size, compile time, and target compatibility.
* [UI Leakage Prevention](ui-leakage-prevention.md) - Patterns that prevent UI frameworks, resources, and presentation assumptions from entering shared KMP modules.
