---
id: kmp.source_sets
type: concept
title: Source Sets
description: Gradle source set groupings that map Kotlin files to compilation targets and define dependency relationships between code layers.
tags: [kotlin-multiplatform, source-sets, gradle, project-structure]
prerequisites:
  - kmp.shared_vs_platform
related:
  - kmp.hierarchical_source_sets
  - kmp.shared_module
  - kmp.test_source_sets
resource: https://kotlinlang.org/docs/multiplatform-discover-project.html#source-sets
timestamp: 2026-01-01
---

## Summary

Source sets are named collections of Kotlin sources and resources compiled together for a specific purpose. In KMP, `commonMain` holds shared code; platform source sets (`androidMain`, `iosMain`) hold target-specific code. Source sets declare dependencies on other source sets, forming a directed graph that Gradle uses to assemble compilations.

## Mental model

A source set is a **compilation slice**, not a module. Files in `commonMain` are compiled into every target. Files in `androidMain` are compiled only for Android and automatically see everything from `commonMain` (via `dependsOn` relationships).

```
commonMain ──dependsOn──► (nothing below)
androidMain ──dependsOn──► commonMain
iosMain ──dependsOn──► commonMain (often via appleMain)
commonTest ──dependsOn──► commonMain
```

The dependency arrow means "I can use code from the source set I depend on." Platform source sets depend on common; common never depends on platform.

## Example

```kotlin
// build.gradle.kts (conceptual)
kotlin {
    sourceSets {
        val commonMain by getting
        val androidMain by getting { dependsOn(commonMain) }
        val iosMain by getting { dependsOn(commonMain) }
        commonMain.dependencies {
            implementation(libs.coroutines.core)
        }
        androidMain.dependencies {
            implementation(libs.ktor.android)
        }
    }
}
```

## Common mistakes

- Putting shared business logic in `androidMain` because "it's easier."
- Circular `dependsOn` between source sets (Gradle rejects or produces confusing errors).
- Adding platform dependencies to `commonMain` dependencies block.
- Confusing source sets with Gradle subprojects (a single KMP module has many source sets).

## Related concepts

- [Hierarchical Source Sets](hierarchical-source-sets.md)
- [Shared Module](shared-module.md)
- [Test Source Sets](test-source-sets.md)
