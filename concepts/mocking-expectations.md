---
id: kmp.mocking_expectations
type: concept
title: Mocking Expectations
description: Fakes, test doubles, and stub strategies for abstractions and expect declarations in KMP tests.
tags: [kotlin-multiplatform, testing, mocking, fakes]
prerequisites:
  - kmp.abstraction_boundaries
  - kmp.expect_declarations
related:
  - kmp.shared_tests
  - kmp.repository_abstraction
resource: https://kotlinlang.org/docs/multiplatform-run-tests.html
timestamp: 2026-01-01
---

## Summary

KMP testing favors **hand-written fakes** over heavyweight mocking frameworks, many of which are JVM-only. Define test doubles in `commonTest` that implement shared interfaces. Avoid mocking `expect` declarations — refactor to interfaces injectable in tests.

## Mental model

```
Production: AuthRepository(SecureStorage) ← AndroidSecureStorage
Test:       AuthRepository(SecureStorage) ← FakeSecureStorage (in-memory map)
```

Mocking frameworks (MockK, Mockito) are largely JVM-specific. For multiplatform tests, use:

- Fake implementations of interfaces.
- `runTest` from `kotlinx-coroutines-test` for coroutines.
- Predefined JSON fixtures for serialization tests.

If code uses `expect` instead of interface, tests cannot easily substitute behavior — architectural smell.

## Example

```kotlin
// commonTest
class FakeSecureStorage : SecureStorage {
    private val map = mutableMapOf<String, String>()
    override suspend fun put(key: String, value: String) { map[key] = value }
    override suspend fun get(key: String) = map[key]
}
```

## Common mistakes

- Using Mockito in commonTest (JVM-only).
- Mocking expect declarations with platform-specific hack actuals for test.
- Fakes that behave differently from real implementations (false positives).
- Not providing fake HTTP clients for networking integration tests in commonTest.

## Related concepts

- [Shared Tests](shared-tests.md)
- [Repository Abstraction](repository-abstraction.md)
