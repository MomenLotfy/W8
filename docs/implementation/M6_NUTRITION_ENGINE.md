# M6 — W8 Nutrition Engine

**Document:** M6_NUTRITION_ENGINE.md
**Product:** W8
**Milestone:** M6
**Status:** Ready for Implementation
**Priority:** P0 — Critical
**Depends On:** M0, M1, M2, M3, M4
**Blocks:** M7, M8, M9, M10, M12, M15

---

# 1. Purpose

M6 implements the W8 Nutrition Engine.

The Nutrition Engine consumes validated and published nutrition content
from the W8 Content Management System.

It is responsible for:

- nutrition plans
- meal plans
- meal schedules
- food/meal items
- nutrition targets
- daily nutrition tracking
- meal completion
- substitutions
- nutrition history
- deterministic nutrition calculations
- user-specific nutrition plan assignments

The engine MUST separate:

1. Nutrition Content
2. Nutrition Plan
3. User Nutrition Assignment
4. Daily Nutrition State
5. Nutrition Logging
6. Nutrition History
7. Nutrition Recommendations

AI recommendations belong to M8/M9 and MUST NOT be mixed into the
deterministic nutrition engine.

---

# 2. Critical Safety Principle

W8 is a fitness and wellness application.

The Nutrition Engine MUST NOT present itself as a medical diagnosis,
medical treatment, or substitute for a qualified healthcare professional.

The system MUST NOT generate unsafe nutritional recommendations.

The system MUST NOT automatically recommend:

- extreme calorie restriction
- dangerous weight-loss rates
- starvation/famine protocols
- treatment for medical conditions
- medication changes
- supplement prescriptions
- eating-disorder treatment
- medical nutrition therapy

Where a request falls outside W8's safe product scope, the system must
respond using the safety behavior defined in Section 13.

---

# 3. Source of Truth

Follow:

1. `/AGENTS.md`
2. `/docs/W8_MASTER_TECHNICAL_SPECIFICATION.md`
3. Section 03 — Domain & Data Model
4. Section 04 — Business Rules & Content
5. Section 06 — API Contract
6. Section 07 — Security Architecture
7. Section 12 — Content Management
8. Section 13 — AI Coach & Personalization
9. Section 20 — Performance
10. This milestone

The Master Specification takes precedence over this document.

---

# 4. Non-Goals

M6 MUST NOT implement:

- AI nutrition generation
- medical diagnosis
- medical diet planning
- AI meal hallucination
- payment logic
- push notification delivery
- CMS UI
- arbitrary user-created nutrition plans
- uncontrolled external food databases

---

# 5. Nutrition Architecture

The architecture MUST follow:

```text
CMS
 │
 ├── Nutrition Plans
 ├── Meals
 ├── Food Items
 └── Nutrition Metadata
        │
        ▼
Nutrition Engine
        │
        ├── Plan Assignment
        ├── Daily Schedule
        ├── Tracking
        ├── Calculations
        └── History
        │
        ▼
Mobile App
```

---

# 6. Published Content Only

Normal users may consume only content that is:

- published
- valid
- available

Draft, rejected, archived, or unauthorized CMS content MUST NOT be exposed through normal user nutrition APIs.

---

# 7. Nutrition Domain

The exact domain model follows Section 03.

Conceptually the engine may contain:

- NutritionPlan
- Meal
- MealItem
- FoodItem
- NutritionValue
- NutritionTarget
- UserNutritionAssignment
- DailyNutrition
- MealLog
- NutritionSummary
- MealSubstitution

Do not create duplicate entities if equivalent entities already exist.

---

# 8. Task List

## M6-T01 — Nutrition Plan Model

Implement the published nutrition plan model.

A plan should contain structured metadata such as:

- id
- name
- localized name
- description
- goal
- duration
- nutrition targets
- meal schedule
- status
- version

Exact fields follow Section 03.

## M6-T02 — Meal Model

Implement structured meals.

Potential fields:

- meal type
- name
- localized name
- description
- items
- nutrition values
- serving information

Do not store the complete meal as an uncontrolled JSON blob if the information needs to be queried or validated.

## M6-T03 — Food/Meal Item Model

Implement the canonical item structure required by Section 03.

Each item must support the nutrition values required by the product.

Examples:

- calories
- protein
- carbohydrates
- fat
- fiber
- serving size
- unit

Only fields defined by the Master Specification should be implemented.

## M6-T04 — Nutrition Target Model

Implement nutrition targets.

Targets may include:

- daily calories
- protein
- carbohydrates
- fat
- water

Only metrics explicitly supported by W8 should exist.

---

# 9. Calculation Rules

All nutrition calculations MUST have a single authoritative implementation.

Do not implement:

```text
Flutter calculation
+
Backend calculation
+
AI calculation
```

for the same metric.

The backend/domain layer is authoritative.

Flutter may display values but should not independently redefine business rules.

---

# 10. Unit Normalization

The backend must use canonical units.

User-facing units may be converted for display.

Example:

```text
Canonical storage
        ↓
Conversion layer
        ↓
Localized presentation
```

Never store the same quantity in multiple inconsistent units.

---

# 11. User Nutrition Assignment

Implement assignment of a published nutrition plan to a user.

An assignment should preserve:

- user
- plan
- plan version
- start date
- end date
- status
- assignment source

The historical assignment must remain reproducible.

If a CMS administrator later publishes version 2 of a plan, an existing user's historical assignment must not silently become version 2.

---

# 12. Daily Nutrition State

Implement daily nutrition state where required.

Conceptually:

```text
Today
 ├── target
 ├── consumed
 ├── remaining
 ├── meals completed
 └── progress
```

The server remains authoritative.

---

# 13. Meal Logging

Users may log meal completion and supported nutrition data.

Requirements:

- authenticated user
- ownership enforcement
- valid meal reference
- valid assignment
- timestamp
- idempotency where required

Do not allow arbitrary nutrition values to replace canonical meal values unless the business specification explicitly supports custom logging.

---

# 14. Meal Completion

Meal completion should have deterministic state.

Example:

- scheduled
- completed
- skipped
- replaced

Exact states follow Section 04.

Invalid transitions must be rejected.

---

# 15. Meal Replacement

If meal replacement is supported:

The replacement MUST be selected from approved compatible content.

The system must validate:

- nutrition compatibility
- meal type
- dietary constraints
- availability

The client MUST NOT simply send:

```text
replace_meal_with_any_id
```

and expect the server to trust it.

---

# 16. Nutrition History

Users can retrieve their own nutrition history.

Requirements:

- pagination
- deterministic ordering
- date filtering
- ownership
- summary metrics
- completed/skipped distinction

Historical nutrition records must remain valid even if CMS content changes.

---

# 17. Nutrition Summary

The backend may calculate:

- daily intake
- target
- remaining
- completion percentage
- meal completion rate

Exact metrics follow Section 04.

Calculations must be deterministic and tested.

---

# 18. User Preferences

The Nutrition Engine must respect preferences defined by the user domain.

Examples may include:

- dietary preference
- excluded foods
- meal preference
- measurement unit

Preferences must NOT automatically be interpreted as medical restrictions.

---

# 19. Dietary Safety

The system MUST distinguish:

- preference
- restriction
- allergy
- medical condition

If allergy information is supported by the product, it must be treated as a safety-critical constraint.

The system must never knowingly recommend a food marked as incompatible with a user's declared allergy.

If reliable allergy handling is not implemented, W8 must NOT claim that it provides allergy-safe meal recommendations.

---

# 20. Nutrition Data Validation

Before publishing a nutrition plan:

Validate:

- required fields
- valid meal references
- valid item references
- valid nutrition values
- serving sizes
- units
- duplicated items
- incompatible configuration

A plan that fails validation MUST NOT be published.

---

# 21. Content Import

Real nutrition data will be imported only after M4 CMS is operational.

Pipeline:

```text
Raw Nutrition Data
      ↓
Normalization
      ↓
Schema Validation
      ↓
Duplicate Detection
      ↓
Import Staging
      ↓
CMS Draft
      ↓
Human Review
      ↓
Approval
      ↓
Publish
```

Never directly insert the raw nutrition dataset into production tables.

---

# 22. AI Boundary

M6 MUST NOT generate nutrition plans using an LLM.

AI-generated nutrition content belongs to M8/M9 and must remain constrained by published W8 nutrition content and safety rules.

---

# 23. API Requirements

Nutrition APIs must enforce:

- authentication
- authorization
- ownership
- published-content validation
- input validation
- pagination
- idempotency
- rate limiting
- consistent errors

---

# 24. Security

Protect against:

- IDOR
- mass assignment
- nutrition-plan tampering
- cross-user logging
- invalid values
- duplicate submissions
- unauthorized plan access
- draft leakage

---

# 25. Performance

Nutrition APIs should avoid:

- N+1 meal queries
- recalculating static nutrition values repeatedly
- unbounded history queries
- unnecessary joins

Published content may be cached.

User-specific state must not be leaked through shared caches.

---

# 26. Events

Emit appropriate domain/application events.

Examples:

- nutrition_plan_assigned
- meal_completed
- meal_skipped
- meal_replaced
- daily_nutrition_completed

Do not call notification providers directly from the Nutrition Engine.

---

# 27. Tests

Required:

## Unit Tests

- nutrition calculations
- unit conversion
- meal state transitions
- replacement validation
- target calculations

## Integration Tests

- assignment
- daily state
- meal logging
- history
- replacement

## Security Tests

- IDOR
- ownership
- unauthorized plan access
- mass assignment
- duplicate logging

## Safety Tests

- invalid nutrition values
- unsafe configurations
- incompatible food restrictions

---

# 28. Definition of Done

- [ ] Nutrition domain implemented
- [ ] Nutrition plan implemented
- [ ] Meal model implemented
- [ ] Food/item model implemented
- [ ] Nutrition targets implemented
- [ ] Published content validation implemented
- [ ] User assignment implemented
- [ ] Plan version preservation implemented
- [ ] Daily nutrition state implemented
- [ ] Meal logging implemented
- [ ] Meal replacement implemented where required
- [ ] Nutrition history implemented
- [ ] Calculations centralized
- [ ] Safety validation implemented
- [ ] Import architecture compatible with CMS
- [ ] Events implemented
- [ ] Security tests pass
- [ ] Integration tests pass
- [ ] CI passes
- [ ] No hardcoded production nutrition content

---

# 29. AI Execution Prompt

Implement W8 M6.

Read:

- AGENTS.md
- Master Specification
- Sections 03, 04, 06, 07, 12, 13, 20
- M0–M5

CRITICAL:

Do not invent nutrition business rules.
Do not generate nutrition plans using an LLM.
Do not import the user's real nutrition dataset yet.
Build the CMS-compatible nutrition engine first.
The backend is the authoritative source for calculations.
Do not allow draft content to reach mobile users.
Preserve nutrition plan versions for historical assignments.
Do not make medical claims.
Implement tests before considering the milestone complete.
If a nutrition rule is ambiguous, STOP and report it.
