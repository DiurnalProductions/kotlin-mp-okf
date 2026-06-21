---
id: kmp.compose_multiplatform_overview
type: concept
title: Compose Multiplatform Overview
description: Conceptual overview of sharing UI with Compose Multiplatform while keeping platform shells and integrations separate.
tags: [kotlin-multiplatform, compose, ui, cmp]
prerequisites:
  - kmp.shared_logic_vs_ui
related:
  - kmp.ui_leakage_prevention
  - kmp.platform_modules
resource: https://www.jetbrains.com/compose-multiplatform/
timestamp: 2026-01-01
---

## Summary

Compose Multiplatform (CMP) extends Jetpack Compose to desktop, iOS, and web, allowing shared UI in `commonMain` alongside shared logic. It is a separate concern from core KMP — this pack treats CMP conceptually. Even with CMP, platform entry points, permissions, and system integrations remain in platform modules.

## Mental model

CMP changes the boundary:

```
Without CMP:  shared logic + platform UI (SwiftUI/Views)
With CMP:     shared logic + shared UI (commonMain) + thin platform shells
```

CMP does not eliminate platform code — it relocates it. Expect `androidMain`/`iosMain` for platform-specific Compose integrations, resource handling, and back gesture behavior. A dedicated Compose pack covers API details; architecturally, treat shared Composables like shared ViewModels: avoid leaking platform types into `@Composable` parameters when possible.

## Example

```kotlin
// commonMain — with CMP (conceptual)
@Composable
fun LoginScreen(viewModel: LoginViewModel, onSuccess: () -> Unit) {
    val state by viewModel.state.collectAsState()
    // shared UI using Compose Multiplatform widgets
}

// iosMain — entry shell still required
// ComposeUIViewController { LoginScreen(...) }
```

## Common mistakes

- Assuming CMP removes all platform-specific UI code.
- Mixing Material (Android-only) with Compose Multiplatform widgets incorrectly.
- Putting CMP in shared module when only one platform uses it (unnecessary constraint).
- Neglecting iOS-specific UX patterns because UI is "shared."

## Related concepts

- [UI Leakage Prevention](ui-leakage-prevention.md)
- [Platform Modules](platform-modules.md)
