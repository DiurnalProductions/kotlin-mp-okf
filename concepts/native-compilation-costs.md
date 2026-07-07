---
type: Concept
title: Native Compilation Costs
description: "Build time, tooling overhead, and developer workflow impact of Kotlin/Native targets in KMP projects."
tags: [kotlin-multiplatform, native, compilation, build-time]
prerequisites:
  - concepts/compilation-targets
related:
  - concepts/startup-time
  - concepts/transitive-constraints
  - concepts/jvm-vs-native-runtime
resource: "https://kotlinlang.org/docs/native-overview.html"
timestamp: 2026-01-01
---

## Summary

Kotlin/Native compilation via LLVM is significantly slower than JVM compilation and produces separate binaries per Native target (device, simulator, architecture). Adding iOS targets multiplies clean build time and CI resource usage. Caching, Gradle configuration cache, and limiting declared targets mitigate cost.

## Mental model

Native compilation is a **fixed tax** on every KMP project with iOS:

- cinterop generation for platform SDKs.
- LLVM bitcode/object compilation per source file per target.
- Linking static or dynamic frameworks for Xcode.

Developer workflow impact: incremental builds help, but clean CI builds can take many minutes for medium projects. Dependency count in `commonMain` directly affects Native compile time and binary size.

## Example

```kotlin
// Mitigation — only declare targets you ship
kotlin {
    iosArm64()           // device
    iosSimulatorArm64()  // simulator (required for dev)
    // avoid iosX64 on Apple Silicon unless needed
}
```

## Common mistakes

- Declaring unused Native targets (macOS, Linux) "just in case."
- Large commonMain dependency graphs bloating Native link step.
- No Gradle remote cache in CI for Native artifacts.
- Running full `linkDebugFrameworkIosArm64` on every commit without change detection.

## Related concepts

- [Startup Time Considerations](startup-time.md)
- [Transitive Constraints](transitive-constraints.md)
- [JVM vs Native Runtime](jvm-vs-native-runtime.md)
