# M3 — W8 Core User Domain

**Document:** M3_CORE_USER_DOMAIN.md
**Product:** W8
**Milestone:** M3
**Status:** Ready for Implementation
**Priority:** P0 — Critical
**Depends On:** M0, M1, M2
**Blocks:** M4, M5, M6, M8, M9, M10, M11, M12

---

# 1. Purpose

M3 implements the core user domain required by the W8 platform.

This milestone establishes the authenticated user's profile, preferences, goals,
fitness context, training availability, measurements, and user-facing settings.

M3 transforms the identity created in M2 into a usable W8 product profile.

The implementation MUST preserve a strict separation between:

- Authentication identity
- User profile
- Fitness goals
- Training preferences
- Nutrition preferences
- Measurements
- Application preferences
- Privacy/settings

---

# 2. Source of Truth

Follow:

1. `/AGENTS.md`
2. `/docs/W8_MASTER_TECHNICAL_SPECIFICATION.md`
3. Section 03 — W8 Domain & Data Model
4. Section 04 — W8 Business Rules & Content Specification
5. Section 06 — W8 API Contract
6. Section 07 — W8 Security Architecture
7. Section 15 — Authentication, Identity & Account Lifecycle
8. Section 20 — Performance & Scalability
9. This milestone

If this document conflicts with the Master Specification,
the Master Specification wins.

---

# 3. Objectives

M3 must implement:

- User profile
- Fitness profile
- Goals
- Training preferences
- Nutrition preferences
- User measurements
- Measurement history
- Application preferences
- Language preference
- Theme preference
- Notification preference foundation
- User onboarding persistence
- Profile update APIs
- Measurement APIs
- Goal management
- User data validation
- User authorization boundaries
- Audit-sensitive profile changes
- Tests

---

# 4. Non-Goals

M3 MUST NOT implement:

- Exercise CMS
- Workout builder
- Workout execution engine
- Nutrition plans
- Meal logging engine
- AI Coach
- Recommendation engine
- Payments
- Push delivery
- Admin content management

Those belong to later milestones.

---

# 5. Domain Boundary

The user domain MUST NOT depend directly on:

- Flutter UI
- Admin UI
- HTTP request objects
- AI provider SDKs
- Payment providers
- Notification providers

The domain must remain independently testable.

---

# 6. User Profile

The profile model should support the fields defined in Section 03.

Potential categories include:

```text
Profile identity
Basic profile information
Fitness context
Training preferences
Nutrition preferences
Goals
Application preferences
```

Only fields explicitly required by the specification should be stored.
Avoid collecting unnecessary personal information.

---

# 7. User Ownership Rule

A normal authenticated user MUST only access or modify their own user-domain resources.

The backend MUST derive the user identity from the authenticated principal.

Never trust:

- `user_id`
- `account_id`
- `owner_id`

from client input when the authenticated identity already determines ownership.

Example:

```text
GET /users/{user_id}/profile
```

must not allow User A to retrieve User B's profile merely by changing the URL.

Where appropriate, use:

```text
GET /me/profile
PATCH /me/profile
```

or equivalent contract defined in Section 06.

---

# 8. Task List

## M3-T01 — User Profile Model

Implement the profile entity and persistence model.

Requirements:

- stable relationship to identity/account
- created timestamp
- updated timestamp
- nullable fields where onboarding may not be complete
- appropriate indexes
- constraints defined by Section 03

Do not duplicate authentication credentials.

## M3-T02 — Profile Retrieval

Implement authenticated profile retrieval.

Requirements:

- authenticated user only
- predictable response schema
- no sensitive authentication fields
- no internal database fields exposed

## M3-T03 — Profile Update

Implement profile updates.

Requirements:

- field validation
- authorization
- partial update support where specified
- immutable fields protected
- audit event for security-sensitive changes

Do not allow the client to update:

- account ID
- user ID
- roles
- permissions
- account status
- subscription status
- security fields

through normal profile APIs.

## M3-T04 — Fitness Goals

Implement the user's fitness goals.

The goal model MUST support the goal types defined in Section 04.

Goals should be represented as structured data, not free-form strings when the business logic depends on them.

Example conceptual categories:

- goal type
- target
- priority
- status
- start date
- target date

Exact fields must follow Section 03.

## M3-T05 — Goal Validation

Goals must be validated against business rules.

Examples:

- impossible target values
- invalid dates
- incompatible target combinations
- invalid ranges

Validation must exist server-side even if Flutter validates locally.

## M3-T06 — Training Preferences

Implement:

- training days
- preferred training time
- activity level
- training preferences

according to the onboarding specification.

The backend MUST validate values against the canonical enums/ranges.

## M3-T07 — Nutrition Preferences

Implement the user's nutrition preferences required by the domain model.

This is preference data.

It MUST NOT be treated as a generated nutrition plan.

Nutrition plans belong to M6/CMS.

## M3-T08 — Measurements

Implement user measurements required by the specification.

Potential examples:

- weight
- height
- body measurements where explicitly supported

Measurements MUST have:

- timestamp
- unit
- normalized storage format
- source where required

## M3-T09 — Measurement History

Users must be able to retrieve their own measurement history.

Requirements:

- deterministic ordering
- pagination where required
- date filtering where specified
- bounded result size

Never expose another user's measurement history.

## M3-T10 — Measurement Business Rules

Implement validation for:

- minimum/maximum supported values
- valid units
- conversion
- chronological consistency where required

Do not silently accept physically impossible values.

## M3-T11 — Onboarding State

Persist onboarding progress.

The onboarding flow MUST be recoverable.

The backend should be able to determine whether the user has:

- not started
- in progress
- completed

or the exact states defined by Section 03/04.

The server MUST remain the source of truth.

## M3-T12 — Application Preferences

Implement user preferences required by the product.

Examples:

- language
- theme
- measurement unit

Exact preferences must follow the Master Specification.

## M3-T13 — Preference Validation

Preferences must use canonical enums rather than arbitrary strings.

Invalid preference values must return a validation error.

## M3-T14 — User Data Export Foundation

Prepare the user-domain layer for the data-export requirement defined in Section 15.

M3 does not need to implement the complete export pipeline unless required by the Master Specification, but all user-owned data must have a clear ownership relationship.

## M3-T15 — Deletion Compatibility

User-domain records must support the account lifecycle defined in M2.

Do not create foreign-key relationships that make account deletion impossible without an intentional lifecycle strategy.

Deletion behavior must follow Section 15.

## M3-T16 — Audit Events

Record relevant changes such as:

- profile updated
- goal changed
- measurement added
- important preference changed

Do not audit every harmless read operation unless explicitly required.

Never store sensitive values unnecessarily inside audit events.

## M3-T17 — API Tests

Tests MUST cover:

**Profile**

- retrieve own profile
- update own profile
- unauthorized access
- invalid update
- immutable-field protection

**Goals**

- create/update goal
- invalid goal
- ownership
- lifecycle behavior

**Measurements**

- valid measurement
- invalid measurement
- unit handling
- history ordering
- ownership

**Onboarding**

- incomplete state
- progress update
- completion
- invalid transitions

**Preferences**

- valid values
- invalid values
- persistence

---

# 9. Data Integrity Requirements

The database MUST enforce integrity wherever practical.

Use:

- foreign keys
- unique constraints
- check constraints where appropriate
- indexes
- NOT NULL constraints where semantically required

Do not rely exclusively on Flutter validation.

---

# 10. Privacy Requirements

The user domain contains personal data.

Therefore:

- minimize collected data
- restrict access
- avoid sensitive logging
- never expose internal identifiers unnecessarily
- enforce ownership server-side
- support lifecycle requirements
- avoid analytics leakage

---

# 11. API Requirements

Every user-domain endpoint must define:

- authentication
- authorization
- request schema
- response schema
- validation
- error behavior
- ownership behavior
- rate-limit classification

No endpoint may expose unrestricted user records.

---

# 12. Performance Requirements

User profile retrieval should be optimized for the common authenticated path.

Avoid:

- N+1 queries
- unnecessary joins
- unbounded history queries
- repeated database calls for static preferences

Indexes must support the expected access patterns.

---

# 13. Definition of Done

- [ ] Profile model implemented
- [ ] Profile API implemented
- [ ] Profile validation implemented
- [ ] Goals implemented
- [ ] Training preferences implemented
- [ ] Nutrition preferences implemented
- [ ] Measurements implemented
- [ ] Measurement history implemented
- [ ] Onboarding state implemented
- [ ] Application preferences implemented
- [ ] Ownership enforcement implemented
- [ ] Audit events implemented
- [ ] Tests pass
- [ ] Security tests pass
- [ ] CI passes
- [ ] API documentation updated
- [ ] Database migrations reviewed

---

# 14. AI Execution Prompt

You are implementing W8 Milestone M3.

Before coding:

- Read /AGENTS.md.
- Read Sections 03, 04, 06, 07 and 15 of the Master Specification.
- Inspect M1 and M2.
- Inspect current migrations.
- Inspect existing domain conventions.

Implement only M3.

Never trust user ownership IDs from client input.

Never allow profile APIs to modify roles, permissions, account status, subscriptions, or authentication credentials.

All business validation must exist server-side.

Add tests for every business rule.

Do not add workout, nutrition-plan, AI, payment, or notification business logic.

After implementation:

- format
- lint
- type-check
- run unit tests
- run integration tests
- run security tests
- inspect migration
- inspect git diff

If the specification is ambiguous, stop and report the ambiguity.

