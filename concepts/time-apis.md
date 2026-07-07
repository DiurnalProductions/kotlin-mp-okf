---
type: Concept
title: Time APIs
description: "Clocks, instants, durations, and time zones in shared KMP code using multiplatform-safe libraries."
tags: [kotlin-multiplatform, time, datetime, kotlinx-datetime]
prerequisites:
  - concepts/abstraction-boundaries
related:
  - concepts/portability-constraints
  - concepts/platform-safe-models
resource: "https://github.com/Kotlin/kotlinx-datetime"
timestamp: 2026-01-01
---

## Summary

Standard Java time APIs (`java.time`, `System.currentTimeMillis()`) are not available in commonMain for all targets. Use `kotlinx-datetime` for `Instant`, `LocalDateTime`, `TimeZone`, and duration handling. Keep time-dependent logic in shared code using these portable types.

## Mental model

Time in shared code should use **semantic types**, not epoch milliseconds from platform clocks. `Instant` represents a point in time; `Clock.System.now()` provides the current instant portably. Time zone conversions belong in shared logic when using `kotlinx-datetime`, avoiding platform locale APIs.

```
commonMain: kotlinx.datetime.Instant, TimeZone, DatePeriod
platform: only if exposing locale-formatted strings to UI (platform layer)
```

Formatting for display (locale-aware date strings) often belongs in UI/platform layers. Storing and comparing timestamps belongs in shared code.

## Example

```kotlin
import kotlinx.datetime.Clock
import kotlinx.datetime.Instant

data class Session(val expiresAt: Instant) {
    fun isExpired(): Boolean = Clock.System.now() > expiresAt
}
```

## Common mistakes

- Using `java.time.*` or `Date` in commonMain.
- Storing formatted date strings instead of `Instant` in domain models.
- Assuming `System.currentTimeMillis()` exists on Native/JS.
- Mixing UTC and local time without explicit `TimeZone` conversion.

## Related concepts

- [Portability Constraints](portability-constraints.md)
- [Platform-Safe Models](platform-safe-models.md)
