---
id: kmp.compilation_targets
type: concept
title: Compilation Targets
description: The distinct backends KMP compiles to — JVM, Native (iOS/macOS), JS/Wasm — and the runtime characteristics each imposes.
tags: [kotlin-multiplatform, compilation, jvm, native, js]
prerequisites:
  - kmp.shared_vs_platform
related:
  - kmp.portability_constraints
  - kmp.jvm_vs_native_runtime
  - kmp.native_compilation_costs
resource: https://kotlinlang.org/docs/multiplatform-dsl-reference.html#targets
timestamp: 2026-01-01
---

## Summary

A KMP project declares **targets** — compilation backends such as `jvm`, `android`, `iosArm64`, `iosSimulatorArm64`, `js`, and `wasmJs`. Each target produces a platform-specific binary or library. Shared code is compiled once per target, with target-specific source sets supplying actual implementations and platform integrations.

## Mental model

Targets are **parallel universes** that share syntax but not runtime. The same Kotlin source file in `commonMain` becomes a JVM class, a Native framework, or a JS module depending on which compiler backend processes it. Target choice drives library availability, performance profile, and debugging workflow.

| Target family | Runtime | Typical use |
|---------------|---------|-------------|
| JVM / Android | JVM + ART | Android apps, server, desktop JVM |
| Native (Kotlin/Native) | LLVM binary | iOS, macOS, embedded |
| JS / Wasm | Browser or Node | Web frontends, KMP in browser |

Not every library supports every target. Target declarations are a **constraint surface** — they define what your shared code must be compatible with.

## Example

```kotlin
// build.gradle.kts (conceptual)
kotlin {
    androidTarget()
    iosArm64()
    iosSimulatorArm64()
    jvm()
    js { browser() }
}
```

Each declared target gets its own compilations, source sets (`androidMain`, `iosMain`, etc.), and artifact output. Adding a target means every shared dependency must support it.

## Common mistakes

- Declaring targets you do not intend to ship, increasing build time and dependency pressure.
- Assuming JVM-only libraries work on Native or JS without checking multiplatform support.
- Forgetting simulator vs device Native targets require separate compilations.
- Conflating `androidTarget()` with `jvm()` — they share JVM bytecode but have different platform APIs and dependency graphs.

## Related concepts

- [Portability Constraints](portability-constraints.md)
- [JVM vs Native Runtime](jvm-vs-native-runtime.md)
- [Native Compilation Costs](native-compilation-costs.md)
