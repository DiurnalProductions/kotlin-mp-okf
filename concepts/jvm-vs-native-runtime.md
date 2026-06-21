---
id: kmp.jvm_vs_native_runtime
type: concept
title: JVM vs Native Runtime
description: Execution model differences between JVM/ART and Kotlin/Native LLVM binaries affecting performance and design.
tags: [kotlin-multiplatform, jvm, native, runtime, performance]
prerequisites:
  - kmp.compilation_targets
related:
  - kmp.memory_model_differences
  - kmp.startup_time
  - kmp.native_compilation_costs
resource: https://kotlinlang.org/docs/native-overview.html
timestamp: 2026-01-01
---

## Summary

JVM targets compile to bytecode executed by a VM with JIT optimization and garbage collection. Kotlin/Native compiles to LLVM machine code with a custom runtime and different GC. Performance characteristics, reflection support, and debugging differ materially between them.

## Mental model

| Aspect | JVM/Android | Kotlin/Native |
|--------|-------------|---------------|
| Execution | Bytecode + JIT | Ahead-of-time machine code |
| GC | JVM/ART GC | Kotlin/Native memory manager |
| Reflection | Full (JVM) | Limited |
| Startup | Warmup benefits | Fast native start, no JIT warmup |
| Binary | JAR/DEX | Framework/binary per arch |

Shared code must not assume JVM performance profiles (e.g., allocation-heavy patterns that JIT optimizes may hurt on Native). Profile on real devices for both targets.

## Example

```kotlin
// Conceptual — allocation pattern
fun processItems(items: List<Item>): List<Result> =
    items.map { transform(it) }  // fine on both; profile hot paths on Native
```

## Common mistakes

- Assuming JVM benchmark results apply to iOS Native.
- Heavy reflection or dynamic class loading in shared code.
- Ignoring DEX method count limits on Android while optimizing Native only.
- Different exception stack trace quality assumed equal for debugging.

## Related concepts

- [Memory Model Differences](memory-model-differences.md)
- [Startup Time Considerations](startup-time.md)
- [Native Compilation Costs](native-compilation-costs.md)
