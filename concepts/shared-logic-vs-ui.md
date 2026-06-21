---
id: kmp.shared_logic_vs_ui
type: concept
title: Shared Logic vs Platform UI
description: Separating presentation from portable business logic so shared modules remain UI-framework agnostic.
tags: [kotlin-multiplatform, ui, architecture, separation]
prerequisites:
  - kmp.shared_vs_platform
  - kmp.abstraction_boundaries
related:
  - kmp.compose_multiplatform_overview
  - kmp.ui_leakage_prevention
  - kmp.clean_architecture_kmp
resource: https://kotlinlang.org/docs/multiplatform-discover-project.html
timestamp: 2026-01-01
---

## Summary

KMP shares **logic**, not presentation by default. ViewModels (without UI framework imports), use cases, and state holders can live in shared code. Actual UI rendering belongs in platform modules using Jetpack Compose, SwiftUI, or web frameworks. The shared layer exposes state and events; UI collects and renders.

## Mental model

```
┌─────────────────────────────────┐
│ Platform UI (Compose/SwiftUI)   │  observes state, dispatches events
├─────────────────────────────────┤
│ Presentation logic (shared)     │  ViewModel, MVI reducer, state machine
├─────────────────────────────────┤
│ Domain (shared)                 │  use cases, entities
└─────────────────────────────────┘
```

Shared presentation logic uses `StateFlow`, sealed event types, and pure state reducers — never `Composable`, `View`, or `UIView`. UI is a thin adapter over shared state.

## Example

```kotlin
// commonMain
data class LoginUiState(val email: String, val loading: Boolean, val error: String?)

class LoginViewModel(private val auth: AuthUseCase) {
    private val _state = MutableStateFlow(LoginUiState("", false, null))
    val state: StateFlow<LoginUiState> = _state.asStateFlow()

    fun onEmailChanged(email: String) { _state.update { it.copy(email = email) } }
    suspend fun login() { /* ... */ }
}
```

## Common mistakes

- Importing Compose or Android resources into commonMain.
- Putting navigation graphs with platform routes in shared code without abstraction.
- Shared ViewModels that reference `Context` or `NavController`.
- Duplicating UI state in platform layer instead of observing shared flows.

## Related concepts

- [Compose Multiplatform Overview](compose-multiplatform-overview.md)
- [UI Leakage Prevention](ui-leakage-prevention.md)
- [Clean Architecture in KMP](clean-architecture-kmp.md)
