---
id: kmp.ui_leakage_prevention
type: concept
title: UI Leakage Prevention
description: Patterns that prevent UI frameworks, resources, and presentation assumptions from entering shared KMP modules.
tags: [kotlin-multiplatform, ui, architecture, boundaries]
prerequisites:
  - kmp.shared_logic_vs_ui
  - kmp.abstraction_boundaries
related:
  - kmp.compose_multiplatform_overview
  - kmp.shared_domain_layer
resource: https://kotlinlang.org/docs/multiplatform-discover-project.html
timestamp: 2026-01-01
---

## Summary

UI leakage occurs when shared modules import UI frameworks, reference drawable/string resources, or encode platform-specific navigation assumptions. Prevention requires strict module boundaries, presentation models that use primitive types and shared enums, and platform-specific string formatting at the UI edge.

## Mental model

The shared module should compile **without any UI dependency on the classpath**. Enforce via:

- Gradle: no Compose/UI deps in `commonMain`.
- Code review: no `@Composable`, `R.string`, `Color`, `Modifier` in shared logic layer.
- Architecture: expose `UiState` with `String` labels or resource keys, resolved in UI.

```
shared: errorCode = ErrorCode.NETWORK_TIMEOUT
android UI: stringResource(R.string.error_network_timeout)
ios UI: NSLocalizedString("error_network_timeout", ...)
```

## Example

```kotlin
// commonMain — no string resources
sealed interface LoginEffect {
    data object NavigateHome : LoginEffect
    data class ShowError(val code: ErrorCode) : LoginEffect
}

// androidMain UI layer
when (effect) {
    is LoginEffect.ShowError -> snackbar.show(getString(effect.code))
}
```

## Common mistakes

- Passing `StringRes` or platform resource IDs in shared state.
- Shared modules depending on `compose.runtime` for non-UI reasons.
- Navigation routes with platform-specific deep link formats in common.
- Color and dimension constants from design system in domain layer.

## Related concepts

- [Compose Multiplatform Overview](compose-multiplatform-overview.md)
- [Shared Domain Layer](shared-domain-layer.md)
