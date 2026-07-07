---
type: Concept
title: Platform Dependencies
description: Target-specific libraries declared in platform source sets that must never leak into commonMain shared code.
tags: [kotlin-multiplatform, dependencies, platform, android, ios]
prerequisites:
  - concepts/gradle-configuration
related:
  - concepts/shared-dependencies
  - concepts/abstraction-boundaries
resource: "https://kotlinlang.org/docs/multiplatform-add-dependencies.html"
timestamp: 2026-01-01
---

## Summary

Platform dependencies are libraries scoped to a single target or target family: OkHttp for Android, Darwin engine for iOS Ktor, AndroidX libraries, or Skia bindings. They are declared in `androidMain`, `iosMain`, or equivalent source set dependency blocks and used only in platform source files.

## Mental model

Platform dependencies are **implementation details** of abstractions defined in shared code. The shared module declares `interface HttpClientFactory`; Android provides one using OkHttp; iOS provides one using NSURLSession via Ktor Darwin.

```
commonMain: interface + shared logic
androidMain: implementation(ktor-client-okhttp) + AndroidHttpClientFactory
iosMain:     implementation(ktor-client-darwin) + IosHttpClientFactory
```

Never re-export platform types through shared APIs. Platform dependencies stay encapsulated in platform source sets.

## Example

```kotlin
// androidMain/build.gradle.kts dependencies block
androidMain.dependencies {
    implementation(libs.ktor.client.okhttp)
    implementation(libs.androidx.security.crypto)
}

// iosMain
iosMain.dependencies {
    implementation(libs.ktor.client.darwin)
}
```

## Common mistakes

- Importing platform dependency types in commonMain via typealiases or expect/actual hacks.
- Using the same engine dependency across all platforms in commonMain (often JVM-only).
- Platform dependencies that pull incompatible versions into the shared compile classpath.
- Duplicating the same platform library in both app module and shared platform source set without alignment.

## Related concepts

- [Shared Dependencies](shared-dependencies.md)
- [Abstraction Boundaries](abstraction-boundaries.md)
