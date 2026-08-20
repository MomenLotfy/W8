
# NUR — Prototype Audit Report & Recommended Flutter Design System Specification

**Subject of audit:** `nur-app-1-2.html` (approved UI/UX prototype, including the Notification Preferences and Admin Dashboard additions)
**Scope:** Visual/interaction reference only. This is an audit — no prototype code was changed to produce it.

---

## PART 1 — PROTOTYPE AUDIT REPORT

### 1. Screen inventory

**Mobile app screens** (driven by `state.productScreen`):

| # | Screen | Sub-views / tabs |
|---|--------|-------------------|
| 1 | Onboarding | 3 phases: Welcome → 5-step Form (goal, weight/height/target, activity level, training days & time, nutrition preference) → Plan Ready |
| 2 | Dashboard | Single view: day-score ring, bento tile grid, quick actions, AI insight card |
| 3 | Workout | 5 sub-views: Plan, Library, Exercise Detail, Logging, Summary |
| 4 | Nutrition | 2 tabs: Today, Plan |
| 5 | AI Coach | 2 tabs: Chat (static transcript), Insights (Daily/Weekly/Monthly) |
| 6 | History | 3 tabs: Overview, Workouts, Progress (with Weight/Strength/Goal sub-tabs) |
| 7 | Notifications | 2 tabs: Preferences, History — reached only via Settings, not in bottom nav |

**Overlays** (bottom sheets / modals — not standalone screens):
Settings sheet · Profile sheet · Premium/paywall sheet · Day Score breakdown sheet · Meal-replace sheet · Exit-Workout confirm (the only centered modal in the app; everything else is a bottom sheet).

**Admin Dashboard** (separate desktop surface, entered via a "Mobile App / Admin Dashboard" switch inside the same App Screens tab): 10 sections — Dashboard, Users, Exercises, Workout Plans, Nutrition, AI Insights, Notifications, Subscriptions, Analytics, Settings.

**Design System reference document** (separate top-level tab, prototype tooling only, not part of the product): 14 documented sections — Color, Typography, Spacing & Radius, Elevation, Buttons, Inputs & Controls, Progress Components, Charts, Navigation, Cards & Status Rows, Premium Lock State, Loading/Empty/Error States, Accessibility, Bilingual LTR/RTL.

**Total:** 7 product screens + 6 overlay surfaces + 10 admin sections + 1 reference document.

---

### 2. Navigation map

```
[Design System]  [App Screens] ← prototype-only chrome, not part of the product
                      │
        ┌─────────────┴─────────────┐
   [Mobile App]              [Admin Dashboard] ← prototype-only chrome
        │                           │
  Bottom nav (5, persistent):  Sidebar (10 sections, no cross-links)
  Dashboard · Workout ·
  AI Coach(●unread) ·
  Nutrition · History

  Onboarding — cold-start only, not reachable from bottom nav
  Notifications — Dashboard/anywhere → Settings sheet → Notifications
                  (back button returns to Settings, reopened on top
                   of whichever screen was active before)

  Workout internal stack:
    Plan → Library → Detail → back to Plan
    Plan → Logging → Summary → Dashboard (NOT back to Plan)
    Logging → Exit-confirm modal → Plan (save or discard)

  Premium sheet: openable from many entry points (locked cards,
  Settings, jump panel) — always closes back to the underlying
  screen; no dedicated "Manage subscription" screen exists.
```

**Observed gap:** Settings ↔ Profile are mutually linked (`settingsFromProfile` / `profileFromSettings`), but Notifications has no path back to Profile — only to Settings, even when Profile was the entry point.

**Admin Dashboard:** sidebar sections are siblings with no deep links between them (e.g., a user row's "View" doesn't navigate to a detail screen — that screen doesn't exist).

---

### 3. Component inventory

| Component | Variants / notes |
|---|---|
| Button | primary, secondary, ghost, danger, icon-only, small, full-width, loading, disabled — 1 base class, 8 modifiers |
| Text input | 2 real `<input>` elements exist **in the whole file**, both isolated in the Design System gallery (text + error state) |
| Stepper | used for all numeric entry in Onboarding/Logging instead of text inputs |
| Toggle switch | `<div>`-based, 8 instances |
| Chip | single/multi-select; `<div>` almost everywhere, one `<span>` instance (added with Notifications) |
| Segmented control | tabs, ranges, previews |
| Card / Card-flat | elevated surface vs. nested flat surface |
| Dash tile | bento-grid dashboard tile |
| List item | icon-badge + text + trailing content, shared row pattern |
| Icon badge | 5 tint variants: neutral, accent, success, warning, danger |
| Status row | icon + label + value + status badge (good/warn/bad) |
| Pro badge / lock overlay | premium-gating pattern, blurs content + centered unlock CTA |
| Ring | circular progress, parametrized radius/stroke/color |
| Bar (linear progress) | thick + thin variants |
| Step progress | segmented dot/bar indicator (onboarding, workout sets) |
| Bar chart | `chart-bars` (admin + design system + insights) |
| Line chart | one-off inline SVG (`lineChartSvg()`), not a reusable function |
| Bottom sheet | 5 instances, each reimplements its own `position:absolute;inset:0` overlay markup rather than sharing one component |
| Centered modal | 1 instance (Exit Workout) |
| Loading / Empty / Error / Offline blocks | shared functions, but only wired into the Workout screen |
| Phone device frame | fixed 375×812 px, prototype chrome only |
| Bottom tab bar | 5 items, pill shape, glass blur |
| Admin sidebar nav item | 10 items, icon + label |
| Admin stat card | value + delta + period |
| Admin data table | header/rows, no pagination |
| Admin status pill | 4 variants: on / off / warn / bad |

**Note:** the app effectively runs two parallel "status" visual languages — icon-badge/pro-badge tints on mobile vs. status-pill on Admin — for the same underlying concept.

---

### 4. Design tokens detected

**Color** (full dark + light remap):
`bg, surface, surface-2, surface-3, border, border-strong, text-primary, text-secondary, text-tertiary, accent (#FF5C00), accent-strong (dark #FF8A47 / light #B33F00), accent-dim, accent-tint, accent-tint-strong, on-accent, success, warning, danger, info` (success/warning/danger each have a ~10–16%-alpha tint pair). `info` is defined for both themes but used exactly once in the entire product (a macro bar color) — effectively dead in the palette.

**Typography:** Display XL 32/40/800, Display LG 24/30/700, Title 18/24/600, Body LG 15/22/500, Body 14/20/400, Caption 12/16/500, Eyebrow 11/14/600 uppercase, Mono XL/LG/MD/SM (tabular figures for all stat numbers). Latin family declared as `'Somar Sans','Inter'`; Arabic as `'Somar Sans','IBM Plex Sans Arabic','Inter'`. Only Inter, IBM Plex Mono and IBM Plex Sans Arabic are actually loaded via the Google Fonts `<link>` — **"Somar Sans" is referenced everywhere as the first-choice font but is never loaded**, so every display/title/body string in the prototype is silently rendering in the Inter fallback.

**Spacing:** 4px base scale — 4, 8, 12, 16, 20, 24, 32, 40, 48, 64.
**Radius:** sm 8, md 12, lg 16, xl 24, pill 999.
**Elevation:** shadow-1 (resting), shadow-2 (sheets/modals), shadow-glow (accent ring / premium moments).
**Motion:** dur-1 120ms, dur-2 220ms, ease `cubic-bezier(.4,0,.2,1)`.

---

### 5. Missing states

- Loading / Empty / Error / Offline are wired **only into the Workout screen** via the global "Demo state" control. Dashboard, Nutrition, AI Coach and History have no loading, empty, error or offline treatment at all, despite these being documented as universal patterns in the Design System reference.
- **Admin Dashboard has zero loading, empty, error, or success states** anywhere, even though the brief for it explicitly calls for "Loading states, Error states, Empty states, Success states." All 10 admin sections show permanently populated static data.
- No "no results" state for any search or filter control (none of them are wired to real filtering logic — see §6).
- No confirmation step on "Sign out" in Settings, unlike Exit Workout, which does have one.
- No validation-error states in the Onboarding flow (steppers can't produce invalid values, so the only input-error demo lives in the disconnected Design System gallery, never in a real flow).
- Notifications History tab is a hardcoded 5-item list — there is no way to see or design its own empty state.
- No push-notification "received" banner/toast state — only the pre-canned in-app History list represents past notifications.

---

### 6. UX inconsistencies

- **No functional text entry exists anywhere in the product.** Every search box (Workout Library, all Admin sections) and the AI Coach message composer is a static `<div class="input">` with a placeholder `<span>` — none of them are real `<input>` elements. The only two real `<input>` elements in the entire file are isolated demos inside the Design System gallery, never used in a live flow.
- Filter buttons ("Status", "Plan", "Muscle group") throughout Admin have no `onclick` — visually present, functionally dead.
- "View" buttons in the Admin Users table and every pencil/edit icon in Admin Exercises/Nutrition/Notifications have no destination — nothing happens on click.
- Admin Users spec calls for a "Suspend/activate account" action; only "View" is implemented.
- Two parallel status-indication systems (icon-badge/pro-badge tints on mobile vs. status-pill on Admin) represent the same concept differently.
- Interactive chips are `<div>` everywhere except the newly added Notifications preview control, which uses `<span>` — same component, two DOM element types.
- Settings→Profile has a return path; Notifications→Profile does not (see §2).
- **The Notifications screen and the entire Admin Dashboard bypass the app's i18n system.** Every other screen routes all copy through `t()`/the 80-key EN/AR dictionary; Notifications uses `t()` once, Admin uses it zero times — confirmed by direct inspection of both code blocks. Practical effect: toggling the language switch will not translate or mirror either surface, while it correctly does so everywhere else in the prototype.

---

### 7. Accessibility issues

- Toggle switches, chips, and segmented-style chip controls are built on non-focusable `<div>`/`<span>` elements with `onclick` instead of `<button>` / `<input type="checkbox">` — they cannot be reached by keyboard Tab, have no `role="switch"`/`aria-checked`, and are invisible to screen-reader "controls" navigation.
- This directly contradicts the Design System's own documented claim ("every interactive element gets a 2px accent outline… never suppressed") — `:focus-visible` cannot fire on an element that was never focusable to begin with.
- Icon-only buttons (close ✕, chevrons, search, filter, pencil/edit) carry no `aria-label`. Only the 5 bottom-nav items are labeled — a screen reader announces every other icon button as unlabeled.
- Onboarding `<label>` elements are not programmatically associated (`for`/`id`) with anything, because the corresponding fields are steppers, not inputs.
- Admin table row-action icons are 32×32 px, below the ≥44×44 px tap-target rule the Design System documents for itself.
- No live-region/announcement pattern exists for asynchronous feedback (e.g., "Preferences saved," an AI-generation error) — the only feedback channel is a visual banner.
- Chart bars (`chart-bars`) convey values purely through pixel height with no numeric value exposed in markup — inaccessible to screen readers and to anyone who can't compare bar heights precisely.

---

### 8. Mobile UX issues

- The phone frame is a fixed 375×812 px box with no responsive scaling logic — it is prototype chrome and does not model how the layout should adapt to real device width/height variation (something Flutter's `MediaQuery`/`LayoutBuilder` will need to own from scratch).
- No text-truncation/ellipsis strategy is defined anywhere for long content (longer Arabic strings, longer user names in Admin tables), so overflow behavior is currently undefined.
- The AI Coach input bar is `position: sticky` with a fade gradient, but a static HTML prototype cannot demonstrate real keyboard-avoidance behavior — flagged as a native-only concern to solve explicitly.
- Onboarding steppers move ±1 per tap with no long-press acceleration, which will be tedious for large ranges (e.g., height 100–220 cm) in a real build.
- Bottom nav uses a fixed 14px inset with no safe-area-aware padding logic — fine inside the fixed device frame, but not representative of real notch/home-indicator handling.

---

### 9. RTL / LTR issues

- The 7 product screens, all 5 sheets, the Exit-Workout modal, the bottom nav, and the Design System document itself correctly use logical CSS properties (`padding-inline`, `inset-inline-start`, `margin-inline-end`) plus a complete, fully-paired 80-key EN/AR dictionary (verified: 0 missing keys in either direction). This part of the app mirrors and translates correctly today.
- **Notifications and Admin Dashboard do not** — both are hardcoded English (see §6), so neither will translate or mirror when Arabic is selected, unlike every other surface.
- Admin Dashboard's own CSS *does* already use logical properties (`border-inline-end`, `text-align:start`), so the layout would mirror correctly the moment the copy is localized — the gap is content, not structure.
- No RTL reference frame exists for the Admin Dashboard anywhere in the Design System document (its Bilingual section only demonstrates itself, never the product or Admin screens).

---

### 10. Dark / Light theme issues

- Full token remapping exists for both themes and is applied consistently across the mobile app, sheets, and the Design System document.
- Admin Dashboard consumes the same CSS variables throughout — confirmed no hardcoded hex colors inside any admin function — so it does theme-switch correctly at the token level.
- `.ring-wrap svg` has a dark-mode-only glow (`filter: drop-shadow(...)`) that is explicitly disabled in light mode — intentional and correct; flag only so this conditional is deliberately replicated in Flutter rather than lost.
- The phone-frame border color changes between themes (#000 dark / #111 light) — prototype-frame-only, not applicable to a real app shell.
- There is a manual Dark/Light toggle in Settings but no "Follow system" option and no demonstrated automatic OS-theme detection.

---

### 11. Features represented visually but not technically specified

- **AI Coach chat** — message list, quick-reply chips, and composer are entirely static; there is no message data model, no send/receive behavior, no typing/streaming indicator defined.
- **Admin search & filter controls** — appearance only; no query, filtering, or result-set logic anywhere.
- **Admin "View"/edit actions** — no destination screens or forms exist for what should happen after clicking them.
- **Notification "Retry" / "Open system settings" buttons** — visual only; the real OS permission APIs they represent are unscoped (expected for a prototype, but should be explicitly scoped before native build).
- **Premium/subscription sheet** — shows Monthly/Yearly price cards with no real payment/IAP flow, no purchase confirmation, no restore-purchases path.
- **Weekly AI Summary card** (Dashboard/History) — static copy with no spec for how or when it's generated/refreshed.
- **Health-data sync row** ("Apple Health" in Settings) — shown pre-connected with a Disconnect action only; no connect/permission-request flow is represented.

---

### 12. Features technically required but missing from the prototype

- Authentication (sign-up, sign-in, password reset) — the prototype opens directly at Onboarding, presuming a session already exists.
- Real form validation (client- and server-style error states) beyond the single disconnected Design System demo.
- OS-level push-notification permission request flow (only in-app "preview" banners simulate it).
- Payment / subscription management: upgrade, downgrade, cancel, billing history, receipts.
- Pagination / incremental loading for Admin tables (currently fixed 3–5 hardcoded rows each, no "load more" pattern).
- Admin authentication and role-gating (the Roles & Permissions panel is display-only — no login-as-role or enforcement pattern).
- **Admin confirmation dialogs** — the Admin UX brief explicitly lists "Confirmation dialogs," but none exist anywhere in Admin; the Exit-Workout modal is the only confirmation dialog in the entire prototype.
- Account deletion / data export flows in Settings.
- Multi-device / active-session management.
- A working "no results" state for search anywhere in the app (none of the search affordances are wired to begin with).

---

## PART 2 — RECOMMENDED FLUTTER DESIGN SYSTEM SPECIFICATION

*(Specification only — no widget code, per instructions.)*

### 2.1 Token layer (`ThemeExtension` / `ThemeData`)

Map every CSS custom property 1:1 to a typed token so both themes stay in lockstep and no screen ever reaches for a raw hex value:

- **`NurColors`** (custom `ThemeExtension<NurColors>`): `bg, surface, surface2, surface3, border, borderStrong, textPrimary, textSecondary, textTertiary, accent, accentStrong, accentDim, accentTint, accentTintStrong, onAccent, success, successTint, warning, warningTint, danger, dangerTint, info, infoTint`. Provide a `light` and `dark` instance; resolve via `Theme.of(context).extension<NurColors>()`. Decide up front whether `info` stays in the system (currently near-unused) or is retired — don't let it linger as dead token debt.
- **`NurSpacing`**: `sp1..sp10` as `double` constants (4→64), consumed as `EdgeInsets`/`SizedBox` gaps — never hardcoded pixel values in widget code.
- **`NurRadius`**: `sm(8), md(12), lg(16), xl(24), pill(999)` as `BorderRadius` constants.
- **`NurElevation`**: `shadow1, shadow2, shadowGlow` as `List<BoxShadow>` constants, theme-aware (glow suppressed in light mode, matching the prototype's conditional).
- **`NurMotion`**: `dur1 = 120ms, dur2 = 220ms`, plus the documented `Curves` equivalent of `cubic-bezier(.4,0,.2,1)` (≈ `Curves.easeInOut` variant, or a custom `Cubic`).
- **`NurTextTheme`**: map `displayXl/displayLg/title/bodyLg/body/caption/eyebrow/monoXl/monoLg/monoMd/monoSm` to Flutter `TextStyle`s inside a single `TextTheme` extension, each with an explicit `fontFeatures: [FontFeature.tabularFigures()]` on the mono styles (this is the correct native equivalent of `font-variant-numeric: tabular-nums`, and matters for every stat number in the app).
- **Fonts:** before implementation, resolve the "Somar Sans never actually loads" gap found in §4 — either license and bundle Somar Sans properly for Flutter, or make Inter the deliberate, documented primary typeface instead of an accidental fallback. Bundle IBM Plex Sans Arabic and IBM Plex Mono as the Arabic and numeral typefaces respectively, matching the prototype's actual (not stated) behavior.

### 2.2 Directionality & localization

- Use Flutter's built-in `Directionality`/`TextDirection` (driven by `Locale`) instead of any manual mirroring logic — this replaces the CSS logical-property approach wholesale and should be simpler, not harder, to get right natively.
- Port the 80-key EN/AR dictionary into an `l10n`/ARB-based setup (`flutter_localizations` + generated `AppLocalizations`) rather than a hand-rolled `STR`/`t()` map — this closes the gap found in §6/§9 by construction, since every new string added to ARB is required to exist in both locales before the build passes.
- **Hard requirement carried over from the audit:** the Notifications and Admin surfaces must be built against this same localization system from day one — do not let them repeat the "hardcoded English" gap found in the prototype.
- Numerals: decide explicitly whether Arabic-locale numerals render as Latin digits (as the prototype's mono styles imply) or localized digits, and encode that choice once in the number-formatting layer (`intl` `NumberFormat`) rather than per-screen.

### 2.3 Component catalog (native equivalents)

| Prototype component | Flutter approach |
|---|---|
| `.btn` variants | One `NurButton` widget family (or `ElevatedButton`/`OutlinedButton`/`TextButton` themed via `ButtonStyle`) with `primary/secondary/ghost/danger` as a style enum; loading state via an internal `CircularProgressIndicator` swap, not a CSS pseudo-class trick |
| Stepper | Custom `NurStepper` widget — keep, but add long-press-to-accelerate (flagged missing in §8) |
| Toggle | `Switch`/`CupertinoSwitch` themed to match, **not** a tappable `Container` — this single choice fixes the largest accessibility gap in §7 for free (real semantics, real focus, real `Semantics(toggled:)`) |
| Chip | `ChoiceChip`/`FilterChip` (single vs. multi-select) instead of a styled `GestureDetector` — same accessibility win as Switch |
| Segmented control | `CupertinoSlidingSegmentedControl` or a themed custom widget backed by `Semantics(container: true)` and per-option `Semantics(selected:)` |
| Card / Card-flat | Two `NurCard` variants (elevated vs. flat) as themed `Container`/`Material` widgets sharing one padding/radius scale |
| List item | `NurListTile` composing an icon badge + text column + trailing slot, replacing bespoke per-screen markup |
| Icon badge | `NurIconBadge` widget parametrized by the 5 tint variants |
| Status row | `NurStatusRow` combining `NurIconBadge` + text + a status pill |
| Pro badge / lock overlay | `NurLockedCard` — wraps arbitrary child content, applies `ImageFilter.blur` + centered unlock CTA, single implementation instead of the prototype's ad hoc per-instance markup |
| Ring | `NurProgressRing` — `CustomPainter` driven by percent/stroke/color, with an accessible `Semantics(value:)` label carrying the numeric percentage (closing the chart-accessibility gap in §7) |
| Linear/step progress | `LinearProgressIndicator` themed, plus a `NurStepProgress` row of segments |
| Bar chart / line chart | One real charting solution (e.g. `fl_chart` or an in-house `CustomPainter` set) replacing both the prototype's ad hoc `chart-bars` divs and the one-off inline SVG line chart — consolidate into a single reusable `NurBarChart`/`NurLineChart` pair, each exposing per-datapoint semantic labels |
| Bottom sheet | Flutter's native `showModalBottomSheet` used for **all five** prototype sheets (Settings, Profile, Premium, Day Score, Meal-replace) via one shared `NurBottomSheet` scaffold — this replaces 5 independent hand-rolled overlay implementations in the prototype with one component, and gets real focus-trapping and back-gesture dismissal for free |
| Centered modal | `showDialog`/`AlertDialog` themed as `NurConfirmDialog`, reused for Exit-Workout **and** for every Admin confirmation dialog required but missing in §12 |
| Loading/Empty/Error/Offline | `NurStateBlock` — a single widget with a `state` enum, deliberately wired into **every** screen (not just Workout, closing §5) via a shared async-state wrapper (e.g. an `AsyncValue`/`Result` pattern feeding one state-block widget per screen) |
| Bottom tab bar | `NavigationBar`/`CupertinoTabBar` themed to match the pill/glass look; badge dot via `Badge` widget |
| Admin sidebar / data table | `NavigationRail` (collapsing to a top bar/drawer under the "Admin UX" responsive breakpoint) + `DataTable`/`PaginatedDataTable` — the latter closes the pagination gap in §12 by construction |
| Admin stat card | `NurStatCard` — value + delta + period, reused across Dashboard/AI Insights/Subscriptions/Analytics rather than four bespoke layouts |

### 2.4 Interaction & state architecture

- Every screen owns an explicit `AsyncValue<T>`-style state (loading / data / empty / error) rather than the prototype's single global demo toggle — this is what makes §5's missing-states gap structurally impossible to reintroduce.
- Search and filter affordances (§6, §11) must be built as real, debounced text-field-driven queries from the start — do not ship another visual-only placeholder.
- Toggle/Chip/Segmented selections should update state through a single source of truth (e.g. Riverpod/Bloc) rather than direct DOM-style mutation, so the same "Enable all / Disable all" and per-category toggle logic in Notifications is trivially testable.
- Notification permission states (not granted / denied / granted) should be modeled against the real platform permission plugin (e.g. `permission_handler`), with the in-app banners in §11 as the UI layer only — not a substitute for the real request/check calls.
- Admin role/permission gating (§12) should be enforced by a route guard, not just represented visually in a Settings panel.

### 2.5 Responsive & platform behavior

- Replace the prototype's fixed 375×812 device frame with real `MediaQuery`/`LayoutBuilder`-driven layout and explicit breakpoints for phone / tablet / (Admin) desktop — the two CSS breakpoints found in §8 (900px, 960px) are a reasonable starting point for where Admin's sidebar collapses and grids reflow, but should be re-validated against actual target device sizes.
- Use `SafeArea` throughout instead of the prototype's fixed 14px bottom-nav inset, so notch/home-indicator handling is correct on every device automatically.
- Text should use `Text.rich`/`overflow: TextOverflow.ellipsis` with explicit `maxLines` wherever the prototype left truncation undefined (§8) — decide this per component once, in the component library, not per screen.
- Keyboard avoidance for the AI Coach composer (§8, §11) should use `Scaffold(resizeToAvoidBottomInset: true)` plus a real `TextField`, closing both the missing-input and keyboard-avoidance gaps in one implementation.

### 2.6 Accessibility baseline (carried over as hard requirements from §7)

- Every interactive control must be a real semantic widget (`Switch`, `Checkbox`, `ChoiceChip`, `Button` family) — never a bare `GestureDetector`/`InkWell` standing in for a control that has a proper Flutter/Material equivalent.
- Every icon-only button gets an explicit `tooltip`/`Semantics(label:)` — not just the 5 bottom-nav items, as in the prototype.
- Minimum tap target 44×44 logical pixels enforced globally (via `kMinInteractiveDimension` or a themed minimum), including Admin table row actions, which fell short of this in the prototype (§7).
- Chart widgets expose `Semantics(value:)`/`Semantics(label:)` per data point, not just visual bar height.
- Adopt a real focus/announcement pattern (`SemanticsService.announce` or a toast/snackbar tied to `Semantics(liveRegion: true)`) for async feedback like "Preferences saved" or generation errors — replacing the prototype's visual-only banners.

---

### Summary of hard carry-overs from audit → spec

1. Build Notifications and Admin on the same localization system as the rest of the app from day one (§6/§9 → §2.2).
2. Wire loading/empty/error/offline into every screen via a shared state pattern, not just Workout (§5 → §2.4).
3. Make every toggle/chip/segmented control a real semantic widget, not a styled tappable box (§7 → §2.3/§2.6).
4. Make search and the AI Coach composer real, functional text input from the start — none of it is functional in the prototype today (§6/§11 → §2.4/§2.5).
5. Add the confirmation-dialog pattern to Admin (delete/suspend/archive) — currently the prototype has exactly one confirmation dialog in total (§12 → §2.3).
6. Resolve the Somar Sans font gap explicitly before implementation — decide and document the real typeface rather than inherit an unnoticed fallback (§4 → §2.1).
