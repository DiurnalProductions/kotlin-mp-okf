---
type: Concept
title: Gradle Configuration
description: "Conceptual model of KMP Gradle plugin setup including targets, source sets, dependencies, and publication."
tags: [kotlin-multiplatform, gradle, build, configuration]
prerequisites:
  - concepts/shared-module
  - concepts/platform-modules
related:
  - concepts/shared-dependencies
  - concepts/platform-dependencies
resource: "https://kotlinlang.org/docs/multiplatform-dsl-reference.html"
timestamp: 2026-01-01
---

## Summary

KMP projects use the `kotlin("multiplatform")` Gradle plugin. The `kotlin { }` block declares targets, configures source set dependencies, and wires compilations. Platform apps are separate Gradle modules that depend on KMP library modules. Understanding this configuration model is essential for correct dependency boundaries.

## Mental model

Gradle configuration in KMP has three layers:

1. **Plugin & targets**: What platforms you compile for.
2. **Source sets & dependsOn**: How code layers stack.
3. **Dependencies per source set**: Which libraries each layer may use.

Configuration is **declarative constraint enforcement**. Putting a dependency in `commonMain` declares "this must work on all targets." The build system validates that at compile time.

## Example

```kotlin
plugins { kotlin("multiplatform") version "2.0.0" }

kotlin {
    androidTarget { publishLibraryVariants("release") }
    iosArm64(); iosSimulatorArm64()
    jvm()

    sourceSets {
        commonMain.dependencies {
            implementation(libs.kotlinx.coroutines.core)
            implementation(libs.kotlinx.serialization.json)
        }
        androidMain.dependencies {
            implementation(libs.ktor.client.okhttp)
        }
        iosMain.dependencies {
            implementation(libs.ktor.client.darwin)
        }
    }
}
```

## Common mistakes

- Declaring dependencies at the root `dependencies { }` block instead of per source set.
- Using `implementation` of JVM-only artifacts in `commonMain`.
- Misconfiguring iOS framework export (static vs dynamic, bundle ID).
- Not aligning Kotlin, Compose, and KMP plugin versions (version catalog helps).

## Related concepts

- [Shared Dependencies](shared-dependencies.md)
- [Platform Dependencies](platform-dependencies.md)
