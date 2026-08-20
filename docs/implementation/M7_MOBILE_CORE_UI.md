# M7 — W8 Mobile Core UI

**Document:** M7_MOBILE_CORE_UI.md
**Product:** W8
**Milestone:** M7
**Status:** Ready for Implementation
**Priority:** P0
**Depends On:** M0, M1, M2, M3, M4, M5, M6
**Blocks:** M8, M9, M10, M11, M15, M16

---

# 1. Purpose

M7 implements the production Flutter application foundation and core user
interface based on the approved W8 prototype.

The prototype is a visual and interaction reference.

It is NOT the implementation source.

The Flutter application must implement the actual architecture defined by:

- Master Specification
- Flutter Architecture
- Business Rules
- API contracts
- Accessibility requirements
- Localization requirements
- Security requirements

---

# 2. Prototype Source

The prototype exists at:

```text
docs/design/index.html
```

The prototype audit report is reference material.

Use it to understand:

- visual hierarchy
- screen inventory
- interaction patterns
- spacing
- typography
- colors
- component states
- navigation
- light/dark themes
- RTL/LTR behavior

Do NOT copy the prototype's technical weaknesses.

Specifically DO NOT reproduce:

- fake inputs
- static search
- DOM-style controls
- hardcoded content
- fixed device dimensions
- CSS overlay architecture
- missing accessibility
- hardcoded English
- global mutable UI state

---

# 3. Required Screens

Implement the production foundation for:

- Onboarding
- Dashboard
- Workout
- Nutrition
- AI Coach shell
- History
- Notifications
- Settings
- Profile
- Premium surfaces

AI functionality itself belongs to M8.

---

# 4. Design System

Implement the token architecture defined in the Master Specification.

Required:

- NurColors
- NurSpacing
- NurRadius
- NurElevation
- NurMotion
- NurTypography

No raw hex values inside screen widgets.
No arbitrary spacing values.
No duplicated button styles.

---

# 5. Font Decision

Before implementation:

Resolve the prototype's Somar Sans issue.

The implementation MUST explicitly choose:

- licensed/bundled Somar Sans

OR

- Inter as the deliberate primary Latin typeface

Arabic typography must use the approved Arabic font.

Do not rely on accidental system fallback.

---

# 6. Localization

Use:

- flutter_localizations
- ARB
- generated AppLocalizations
- intl

Supported languages:

- English
- Arabic

Every user-facing string MUST be localized.

Notifications and Admin APIs must follow the same localization strategy.

No hardcoded UI strings.

---

# 7. RTL

RTL must be controlled by locale.

Use:

- Directionality
- TextDirection
- AlignmentDirectional
- EdgeInsetsDirectional

Never manually reverse icons/layouts based on language unless the icon itself has semantic directional meaning.

---

# 8. Theme

Support:

- Light
- Dark
- System

Theme selection must be persisted.

No screen may bypass the theme token layer.

---

# 9. Responsive Layout

The application MUST support real devices.

Do not use:

```text
375 × 812
```

as a layout assumption.

Use:

- MediaQuery
- LayoutBuilder
- SafeArea

Handle:

- small phones
- standard phones
- large phones
- tablets where applicable
- landscape where supported

---

# 10. Navigation

Implement typed/navigation-safe routing.

Required conceptual routes:

- /onboarding
- /dashboard
- /workout
- /nutrition
- /coach
- /history
- /notifications
- /settings
- /profile

Exact route strategy follows Section 10.

Navigation guards must integrate with authentication state.

---

# 11. Bottom Navigation

Implement five persistent destinations:

- Dashboard
- Workout
- AI Coach
- Nutrition
- History

Notifications is NOT a bottom-nav destination.

Unread notification state must be represented by the approved badge mechanism.

---

# 12. Bottom Sheets

Create one reusable:

- NurBottomSheet

for:

- Settings
- Profile
- Premium
- Day Score
- Meal Replacement

Do not create five independent implementations.

---

# 13. Confirmation Dialog

Create:

- NurConfirmDialog

Use for:

- exit workout
- destructive account actions
- future administrative confirmation where applicable

---

# 14. Component Catalog

Implement reusable components for:

- NurButton
- NurInput
- NurStepper
- NurSwitch
- NurChip
- NurSegmentedControl
- NurCard
- NurListTile
- NurIconBadge
- NurStatusRow
- NurLockedCard
- NurProgressRing
- NurProgressBar
- NurStepProgress
- NurBarChart
- NurLineChart
- NurBottomSheet
- NurConfirmDialog
- NurStateBlock
- NurEmptyState
- NurErrorState
- NurLoadingState
- NurOfflineState

Components must be generic enough for reuse.

---

# 15. Accessibility

Every interactive control must use a semantic Flutter widget.

Minimum tap target:

- 44 × 44 logical pixels

Every icon-only button must have:

- tooltip
- Semantics(label:)

Charts must expose meaningful semantic values.

Async feedback must be announced.

---

# 16. State Management

Use the state-management architecture defined in Section 10.

Do not place business logic directly inside widgets.

UI should consume:

- state
- events
- commands
- view models/providers

depending on the chosen architecture.

---

# 17. Async State

Every network-backed screen must support:

- loading
- success
- empty
- error
- offline

Do not assume API requests always succeed.

---

# 18. Real Inputs

Implement actual:

- TextField
- TextFormField

for:

- search
- AI composer
- relevant onboarding inputs
- profile editing

Do not use fake containers as inputs.

---

# 19. Keyboard Handling

Use:

- Scaffold
- resizeToAvoidBottomInset
- SafeArea
- Scrollable content

The AI Coach composer must remain usable when the keyboard appears.

---

# 20. Offline Behavior

Where offline behavior is required:

- detect connectivity
- display offline state
- preserve safe local state
- retry
- reconcile with backend

Never claim that a mutation was permanently saved if it has not reached the authoritative backend.

---

# 21. Error Handling

API errors must be mapped to user-friendly localized messages.

Do not expose:

- stack traces
- database errors
- SQL
- internal service names
- authentication internals

---

# 22. Loading UX

Use skeleton/loading components where appropriate.

Avoid indefinite spinners.

Every async operation must have:

- loading
- success
- failure
- retry

behavior where applicable.

---

# 23. Screen Implementation Order

Implement in this order:

1. App shell
2. Theme
3. Localization
4. Routing
5. Authentication gate
6. Bottom navigation
7. Shared components
8. Onboarding
9. Dashboard
10. Workout
11. Nutrition
12. History
13. Notifications
14. Settings
15. Profile
16. Premium surfaces
17. AI Coach shell

---

# 24. API Integration

Do not use mock data in production application code.

For development, use controlled fixtures only where explicitly permitted by the testing architecture.

API clients must use:

- typed DTOs
- centralized error mapping
- authentication handling
- request cancellation where appropriate
- retry policy only for safe operations

---

# 25. Security

Never store secrets in Flutter source code.

Do not put:

- API private keys
- database credentials
- LLM provider secrets
- payment secrets
- admin secrets

inside the application.

---

# 26. Performance

Avoid:

- unnecessary rebuilds
- huge widget trees
- unbounded lists
- decoding huge media synchronously
- duplicate API requests

Use lazy lists and image caching where appropriate.

---

# 27. Tests

Required:

## Widget Tests

- buttons
- switches
- chips
- forms
- navigation
- states

## Integration Tests

- onboarding
- dashboard loading
- workout navigation
- nutrition
- notifications
- settings

## Accessibility Tests

- semantics
- labels
- minimum tap targets
- RTL
- text scaling

## Golden Tests

For important design-system components where the team decides golden tests are appropriate.

---

# 28. Definition of Done

- [ ] Design system implemented
- [ ] Fonts resolved
- [ ] Theme implemented
- [ ] Localization implemented
- [ ] RTL implemented
- [ ] Routing implemented
- [ ] SafeArea implemented
- [ ] Bottom navigation implemented
- [ ] Core screens implemented
- [ ] Bottom sheets implemented
- [ ] Confirmation dialog implemented
- [ ] Real input controls implemented
- [ ] Async states implemented
- [ ] Accessibility baseline implemented
- [ ] API layer integrated
- [ ] No production mock data
- [ ] Tests pass
- [ ] Flutter analyze passes
- [ ] Formatting passes
- [ ] Integration tests pass

---

# 29. AI Execution Prompt

Implement W8 M7.

Read:

- AGENTS.md
- Master Specification
- Section 10
- Section 02
- Section 06
- Section 07
- Section 08
- M0–M6
- docs/design/index.html
- prototype audit report

The HTML prototype is a visual reference only.
Do not copy its implementation patterns.
Do not use fake inputs.
Do not hardcode content.
Do not use mock data as production data.
Do not implement business logic inside widgets.
Build reusable components before duplicating UI.
Validate RTL, light/dark mode, accessibility, keyboard behavior and different device sizes.

Run:

```text
flutter analyze
flutter test
```

before completing the milestone.

Do not continue when architectural ambiguity exists.
