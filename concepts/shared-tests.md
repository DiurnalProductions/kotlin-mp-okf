---
id: kmp.shared_tests
type: concept
title: Shared Tests
description: Unit and integration tests in commonTest that verify portable logic runs correctly on all supported targets.
tags: [kotlin-multiplatform, testing, commonTest, unit-tests]
prerequisites:
  - kmp.shared_module
  - kmp.source_sets
related:
  - kmp.platform_specific_tests
  - kmp.test_source_sets
  - kmp.mocking_expectations
resource: https://kotlinlang.org/docs/multiplatform-run-tests.html
timestamp: 2026-01-01
---

## Summary

`commonTest` holds tests for code in `commonMain`. These tests run on every platform's test runner (JVM test, iOS simulator test, JS test) using multiplatform test libraries like `kotlin-test`. Shared tests validate domain logic, use cases, serializers, and state machines once, executed per target.

## Mental model

Shared tests are **logic verification multiplied by targets**. A passing `commonTest` on JVM does not guarantee iOS test pass if platform-specific actuals affect behavior — but pure domain tests should pass everywhere identically.

```
commonMain code ← tested by → commonTest
platform actuals ← tested by → platform test sets (when needed)
```

Maximize coverage in `commonTest` with fakes for abstractions. Minimize platform test duplication.

## Example

```kotlin
// commonTest
class AuthUseCaseTest {
    private val fakeStorage = FakeSecureStorage()
    private val useCase = AuthUseCase(fakeStorage)

    @Test
    fun savesTokenOnLogin() = runTest {
        useCase.login("user", "pass")
        assertEquals("token123", fakeStorage.get("token"))
    }
}
```

## Common mistakes

- Testing only on JVM and never running `iosSimulatorArm64Test`.
- Using JUnit-only APIs in commonTest without `kotlin-test` adapters.
- Platform mocks in commonTest that import Android SDK.
- Skipping shared tests for serializer round-trips and domain invariants.

## Related concepts

- [Platform-Specific Tests](platform-specific-tests.md)
- [Test Source Sets](test-source-sets.md)
- [Mocking Expectations](mocking-expectations.md)
