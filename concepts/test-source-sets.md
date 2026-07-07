---
type: Concept
title: Test Source Sets
description: "commonTest, androidUnitTest, iosTest, and hierarchical test source set wiring for KMP projects."
tags: [kotlin-multiplatform, testing, source-sets, gradle]
prerequisites:
  - concepts/source-sets
  - concepts/shared-tests
related:
  - concepts/platform-specific-tests
  - concepts/gradle-configuration
resource: "https://kotlinlang.org/docs/multiplatform-set-up-tests.html"
timestamp: 2026-01-01
---

## Summary

KMP mirrors production source sets with test source sets. `commonTest` depends on `commonMain`. Platform test sets (`androidUnitTest`, `iosSimulatorArm64Test`, `jvmTest`) depend on their corresponding main sets and inherit `commonTest` dependencies when configured.

## Mental model

```
commonTest ──dependsOn──► commonMain
androidUnitTest ──dependsOn──► androidMain (+ commonTest)
iosSimulatorArm64Test ──dependsOn──► iosSimulatorArm64Main
```

Test dependencies (`kotlin-test`, `coroutines-test`) belong in test source sets, not `commonMain`. Shared test utilities can live in `commonTest` and be reused by platform tests via `dependsOn(commonTest)`.

## Example

```kotlin
kotlin {
    sourceSets {
        val commonTest by getting {
            dependencies {
                implementation(kotlin("test"))
                implementation(libs.coroutines.test)
            }
        }
        val androidUnitTest by getting {
            dependsOn(commonTest)
        }
    }
}
```

## Common mistakes

- Test dependencies in `commonMain`.
- Forgetting to wire `dependsOn(commonTest)` for platform test sets.
- Not running iOS tests in CI (`./gradlew iosSimulatorArm64Test`).
- Duplicate test utility code across platform test sets instead of sharing via commonTest.

## Related concepts

- [Platform-Specific Tests](platform-specific-tests.md)
- [Gradle Configuration](gradle-configuration.md)
