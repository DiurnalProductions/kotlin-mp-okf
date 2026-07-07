---
type: Concept
title: Shared Dependencies
description: Multiplatform libraries declared in commonMain that must compile and link for every declared target.
tags: [kotlin-multiplatform, dependencies, libraries, commonMain]
prerequisites:
  - concepts/gradle-configuration
related:
  - concepts/transitive-constraints
  - concepts/portability-constraints
  - concepts/binary-compatibility
resource: "https://kotlinlang.org/docs/multiplatform-add-dependencies.html"
timestamp: 2026-01-01
---

## Summary

Shared dependencies are libraries added to `commonMain` (or other widely-shared source sets). They must publish Kotlin Multiplatform artifacts with metadata for all project targets. Examples include `kotlinx-coroutines-core`, `kotlinx-serialization-json`, `kotlinx-datetime`, and Ktor client core.

## Mental model

A `commonMain` dependency is a **universal commitment**. If any target lacks a compatible artifact, the entire project fails to compile for that target. Before adding a dependency to common, verify:

- Does it publish `-common`, `-jvm`, `-iosarm64`, etc. variants?
- Is the API surface multiplatform-safe (no `java.*`)?
- Does its transitive graph also support all targets?

Prefer libraries from the **Kotlin Foundation** and established KMP ecosystem. When a library is JVM-only, add it to `jvmMain` or `androidMain` only and abstract its usage behind a shared interface.

## Example

```kotlin
// libs.versions.toml (conceptual)
[libraries]
coroutines-core = { module = "org.jetbrains.kotlinx:kotlinx-coroutines-core", version = "1.8.0" }
serialization-json = { module = "org.jetbrains.kotlinx:kotlinx-serialization-json", version = "1.6.0" }
datetime = { module = "org.jetbrains.kotlinx:kotlinx-datetime", version = "0.5.0" }

// commonMain — safe shared deps
commonMain.dependencies {
    implementation(libs.coroutines.core)
    implementation(libs.serialization.json)
    implementation(libs.datetime)
}
```

## Common mistakes

- Adding Retrofit, Room, or other JVM/Android-only libraries to commonMain.
- Assuming a library "works everywhere" because it is written in Kotlin.
- Version skew between coroutines, serialization, and Kotlin compiler.
- Pulling in heavy transitive dependencies that bloat Native binaries.

## Related concepts

- [Transitive Constraints](transitive-constraints.md)
- [Portability Constraints](portability-constraints.md)
- [Binary Compatibility](binary-compatibility.md)
