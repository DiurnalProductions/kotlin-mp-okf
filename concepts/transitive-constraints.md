---
type: Concept
title: Transitive Constraints
description: "How dependency graphs propagate through KMP source sets and the impact on binary size, compile time, and target compatibility."
tags: [kotlin-multiplatform, dependencies, transitive, gradle]
prerequisites:
  - concepts/shared-dependencies
related:
  - concepts/binary-compatibility
  - concepts/native-compilation-costs
resource: "https://kotlinlang.org/docs/multiplatform-share-libraries.html"
timestamp: 2026-01-01
---

## Summary

Transitive dependencies are libraries pulled in automatically by direct dependencies. In KMP, a transitive dependency of a `commonMain` library must also support all targets, or resolution fails. Transitive graphs affect Native binary size, JS bundle size, and compile time across all targets.

## Mental model

Adding one `commonMain` dependency can import a **tree** of libraries. Each node in the tree must have artifacts for every target. A JVM-only transitive dependency hidden deep in the graph breaks iOS compilation.

```
commonMain → LibraryA → LibraryB → LibraryC (JVM-only) 💥 iOS fails
```

Mitigation strategies:

- Use Gradle dependency insight / `./gradlew :shared:dependencies` per source set.
- Exclude transitive dependencies that are not multiplatform.
- Choose libraries with minimal, KMP-clean dependency graphs.
- Use `api` vs `implementation` carefully when publishing shared libraries.

## Example

```kotlin
commonMain.dependencies {
    implementation(libs.ktor.client.core) {
        // Conceptual — verify transitive graph is KMP-clean
    }
}
// ktor-client-core is multiplatform; engine is platform-specific (declared per target)
```

## Common mistakes

- Ignoring transitive dependencies until a new target is added.
- Using `api` in a published KMP library, forcing all consumers to inherit heavy transitives.
- Not checking that annotation processors or compiler plugins are target-appropriate.
- Assuming Gradle's JVM resolution success implies Native/JS success.

## Related concepts

- [Binary Compatibility](binary-compatibility.md)
- [Native Compilation Costs](native-compilation-costs.md)
