---
id: kmp.platform_specific_tests
type: concept
title: Platform-Specific Tests
description: androidTest, iosTest, and target-specific tests for actual implementations, integrations, and platform SDK behavior.
tags: [kotlin-multiplatform, testing, androidTest, iosTest]
prerequisites:
  - kmp.platform_modules
  - kmp.shared_tests
related:
  - kmp.test_source_sets
  - kmp.mocking_expectations
resource: https://kotlinlang.org/docs/multiplatform-run-tests.html
timestamp: 2026-01-01
---

## Summary

Platform-specific tests verify actual implementations, SDK integrations, and behaviors that cannot run in `commonTest`. Android uses instrumented tests (`androidInstrumentedTest`). iOS runs XCTest-compatible tests via Kotlin/Native test executables. JVM/desktop has `jvmTest`.

## Mental model

Platform tests answer: **"Does our platform binding work correctly?"** not "Does our business logic work?" Business logic belongs in `commonTest`. Platform tests cover Keychain storage, EncryptedSharedPreferences, camera permission flows, and Compose screenshot tests.

```
commonTest: AuthUseCase with FakeStorage  ✓ all targets
androidInstrumentedTest: AndroidSecureStorage on device  ✓ Android only
```

## Example

```kotlin
// androidInstrumentedTest
@Test
fun secureStorage_persistsAcrossProcess() {
    val storage = AndroidSecureStorage(InstrumentationRegistry.getInstrumentation().targetContext)
    runBlocking { storage.put("key", "value") }
    assertEquals("value", runBlocking { storage.get("key") })
}
```

## Common mistakes

- Duplicating domain logic tests in every platform test set.
- No iOS test execution in CI (only JVM `check`).
- Instrumented tests for logic that could use fakes in commonTest.
- Platform tests that depend on network without hermetic fixtures.

## Related concepts

- [Test Source Sets](test-source-sets.md)
- [Mocking Expectations](mocking-expectations.md)
