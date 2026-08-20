# M5 — W8 Workout Engine

**Document:** M5_WORKOUT_ENGINE.md
**Product:** W8
**Milestone:** M5
**Status:** Ready for Implementation
**Priority:** P0 — Critical
**Depends On:** M0, M1, M2, M3, M4
**Blocks:** M7, M8, M9, M10, M12, M15

---

# 1. Purpose

M5 implements the W8 workout domain and execution engine.

The Workout Engine consumes PUBLISHED workout content created through the CMS.

It is responsible for turning workout plans into user-specific workout sessions,
tracking execution, recording sets, calculating progress, and producing
historical workout records.

The engine MUST separate:

```text
Workout Content
Workout Prescription
Workout Session
Workout Execution
Workout History
```

---

# 2. Source of Truth

Follow:

1. `/AGENTS.md`
2. Master Specification
3. Section 03 — Domain & Data Model
4. Section 04 — Business Rules
5. Section 06 — API Contract
6. Section 07 — Security
7. Section 12 — Workout Builder
8. Section 13 — Personalization
9. Section 20 — Performance
10. This document

---

# 3. Non-Goals

M5 MUST NOT implement:

- AI recommendations
- automatic personalization engine
- payments
- nutrition calculations
- push notifications
- CMS editing UI
- arbitrary exercise creation from mobile

---

# 4. Core Principle

The workout engine MUST NOT mutate the original CMS workout definition when a user starts or modifies a workout.

Instead:

```text
Published Workout Plan
        ↓
Workout Prescription/Snapshot
        ↓
User Workout Session
        ↓
Execution Records
```

Historical workout data must remain reproducible even if CMS content changes later.

---

# 5. Workout Lifecycle

The exact state machine follows Section 03/04.

Conceptually:

```text
Scheduled
   ↓
Available
   ↓
In Progress
   ├── Completed
   └── Abandoned
```

Additional states may be required by the Master Specification.

Invalid transitions MUST be rejected.

---

# 6. Task List

## M5-T01 — Workout Domain Model

Implement domain entities for:

- Workout Plan
- Workout Session/Day
- Exercise Prescription
- Set Prescription
- Workout Session
- Set Execution
- Workout Summary

Exact entities follow Section 03.

## M5-T02 — Published Content Resolution

Workout sessions may only resolve valid published content.

Do not allow normal users to execute:

- drafts
- archived content
- invalid versions

unless historical access is explicitly required.

## M5-T03 — Workout Snapshot

When required by the architecture, create an immutable reference/snapshot of the workout prescription used by a user.

The snapshot must preserve enough information to explain what the user was actually assigned at that time.

## M5-T04 — Workout Session Creation

Implement starting a workout.

Requirements:

- authenticated user
- valid workout assignment
- valid content
- create session
- initialize state
- prevent accidental duplicate active sessions

## M5-T05 — Workout Session Retrieval

Users can retrieve their own active/current workout.

The API MUST NOT expose another user's session.

## M5-T06 — Exercise Execution

Implement execution of prescribed exercises.

A session should maintain:

- exercise order
- exercise identity/reference
- prescription
- execution state

Do not allow the client to arbitrarily inject an exercise into a prescribed workout unless the business rules explicitly allow substitutions.

## M5-T07 — Set Logging

Implement set logging.

Depending on Section 03/04, support fields such as:

- repetitions
- weight
- duration
- distance
- RPE/RIR
- completion
- timestamp

Only metrics applicable to the exercise type should be accepted.

## M5-T08 — Set Validation

Validate:

- repetitions
- weight
- duration
- distance
- RPE/RIR
- ordering
- duplicate set submissions

Prevent impossible or malicious values.

Client validation is not sufficient.

## M5-T09 — Idempotency

Workout logging operations must support safe retries.

For example:

- mobile sends set
- network times out
- mobile retries
- must not create duplicate execution records

Use an idempotency mechanism defined by Section 06.

## M5-T10 — Pause/Resume

If supported by the Master Specification, implement:

- pause
- resume

The server must maintain authoritative state.

## M5-T11 — Complete Workout

Implement completion.

Requirements:

- validate session state
- validate required execution data
- finalize session
- calculate summary
- persist completion timestamp
- create relevant events

## M5-T12 — Abandon Workout

Implement abandonment according to business rules.

If a user exits unexpectedly, the system must distinguish:

- completed
- abandoned
- in-progress

Do not convert an incomplete workout into a successful workout.

## M5-T13 — Workout Summary

Generate a deterministic workout summary.

Potential metrics:

- duration
- completed exercises
- completed sets
- volume
- repetitions
- personal records where defined
- completion percentage

Exact metrics follow Section 04.

---

# 7. Exercise Substitution

If substitutions are supported:

The system must validate:

- substitution eligibility
- muscle compatibility
- equipment compatibility
- difficulty
- movement pattern

Do not allow arbitrary client-side substitution.

All substitutions must be recorded for history and analytics.

---

# 8. Progress & Personal Records

If defined by Section 04:

Support deterministic calculation of:

- volume
- best weight
- best repetitions
- estimated strength
- personal record

The calculation MUST be centralized.

Do not implement the same formula differently in Flutter and backend.

---

# 9. Workout History

Users must be able to retrieve historical workouts.

Requirements:

- own data only
- pagination
- deterministic ordering
- date filtering where supported
- summary information
- completed/abandoned distinction

Historical records MUST remain valid if CMS content changes.

---

# 10. Workout Progression

The engine must support progression rules defined by Section 04.

Examples may include:

- increase reps
- increase load
- increase sets
- progress difficulty
- deload

M5 should implement the deterministic workout rules.

AI-generated recommendations belong to M8/M9.

---

# 11. Workout Assignment

Workout plans may be assigned to users through the mechanisms defined by the Master Specification.

Assignment must preserve:

- plan/version
- start date
- status
- user

Do not overwrite historical assignments when a new plan becomes active.

---

# 12. Concurrency

Protect against:

- double session creation
- duplicate set submission
- simultaneous completion
- simultaneous update

Use appropriate database transactions and constraints.

---

# 13. API Requirements

Workout APIs must enforce:

- authentication
- ownership
- content availability
- state transitions
- validation
- idempotency
- rate limits

---

# 14. Security Requirements

Protect against:

- IDOR
- mass assignment
- duplicate submissions
- tampering with prescription
- forged completion
- invalid set values
- cross-user session access
- historical record modification

Users MUST NOT modify historical workout records unless the specification explicitly allows a controlled correction workflow.

---

# 15. Performance Requirements

Workout APIs are high-frequency.

Avoid:

- database query per set
- unnecessary full-plan reloads
- N+1 exercise queries
- unbounded history queries

Use transactions appropriately.

Consider batching where the API contract supports it.

---

# 16. Event Integration

M5 should emit domain/application events where required.

Examples:

- workout_started
- set_logged
- workout_completed
- workout_abandoned
- personal_record_achieved

Events should be consumed by later systems such as:

- notifications
- analytics
- personalization
- AI Coach

M5 itself MUST NOT directly contain notification-provider logic.

---

# 17. Tests

**Unit**

Test:

- state transitions
- set validation
- progression
- summary calculations
- PR calculations
- substitution rules

**Integration**

Test:

- session creation
- set logging
- idempotency
- completion
- abandonment
- history

**Security**

Test:

- IDOR
- ownership
- mass assignment
- forged completion
- duplicate request
- cross-user access

**Concurrency**

Test:

- duplicate session creation
- duplicate set logging
- simultaneous completion

---

# 18. API Contract Tests

For every workout endpoint verify:

- request schema
- response schema
- authentication
- authorization
- validation
- error responses
- idempotency
- pagination

---

# 19. Mobile Integration Requirements

The Flutter application must treat backend workout state as authoritative.

The client may optimistically update UI where appropriate, but final workout state must be reconciled with the server.

Offline support, if defined by Section 20/23, must not create conflicting authoritative records.

---

# 20. Definition of Done

- [ ] Workout domain implemented
- [ ] Published content resolution implemented
- [ ] Workout snapshot/version handling implemented
- [ ] Session lifecycle implemented
- [ ] Exercise execution implemented
- [ ] Set logging implemented
- [ ] Validation implemented
- [ ] Idempotency implemented
- [ ] Pause/resume implemented if required
- [ ] Completion implemented
- [ ] Abandonment implemented
- [ ] Summary implemented
- [ ] History implemented
- [ ] Progression rules implemented
- [ ] Substitution implemented if required
- [ ] Events implemented
- [ ] Security tests pass
- [ ] Concurrency tests pass
- [ ] API tests pass
- [ ] CI passes
- [ ] No hardcoded workout content
- [ ] Historical workout integrity verified

---

# 21. AI Execution Prompt

You are implementing W8 Milestone M5 — Workout Engine.

Before coding:

- Read /AGENTS.md.
- Read Sections 03, 04, 06, 07, 12, 13 and 20.
- Inspect M1, M2, M3 and M4.
- Inspect workout migrations and CMS models.
- Understand the published-content lifecycle.

CRITICAL RULES:

The CMS is the source of workout content.

Never hardcode workout plans in the backend or Flutter.

Never mutate published CMS content when a user starts a workout.

Historical workout execution must remain reproducible.

All workout ownership must be enforced server-side.

All set logging must be idempotent.

All state transitions must be validated server-side.

Do not put business calculations in Flutter.

Do not duplicate progression formulas across services.

Do not allow users to forge workout completion.

Do not trust client-provided user IDs.

Do not bypass CMS publication status.

After implementation:

- run unit tests
- run integration tests
- run security tests
- run concurrency/idempotency tests
- run migration validation
- run lint/type checks
- inspect API contract
- inspect database indexes
- inspect git diff

If any workout rule is ambiguous, STOP and report the ambiguity before creating a permanent database or business-rule assumption.
