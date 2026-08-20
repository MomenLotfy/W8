# W8 — M15 Testing, QA & Release Validation

Status: Implementation Specification
Milestone: M15

---

## 1. Objective

Establish a production-grade testing system covering:

- Backend
- Flutter
- Admin
- Database
- APIs
- Authentication
- Payments
- AI
- Notifications
- Security
- Infrastructure

The objective is not maximum test count.

The objective is reliable behavior and controlled regressions.

---

## 2. Testing Pyramid

W8 follows:

```text
             E2E
          /       \
     Integration   Contract
       /             \
   Unit             Component
```

Most tests should be fast unit tests.

---

## 3. Test Environments

Required:

### Local
Developer/AI implementation.

### CI
Automated verification.

### Staging
Production-like environment.

### Production
Real system.

Tests must never accidentally target production.

---

## 4. Backend Unit Tests

Test:

- domain rules
- business services
- validators
- state transitions
- entitlement logic
- workout calculations
- nutrition calculations
- personalization rules
- notification rules

Unit tests must not require external services unless explicitly testing integration.

---

## 5. Backend Integration Tests

Test:

- API + database
- authentication
- authorization
- Redis
- background workers
- file storage
- webhook processing

Use isolated test infrastructure.

---

## 6. API Contract Tests

Every API contract defined in:

`W8_MASTER_TECHNICAL_SPECIFICATION.md`

must be tested.

Verify:

- request schema
- response schema
- status code
- validation errors
- authentication behavior
- authorization behavior

Breaking API changes must fail CI unless explicitly approved.

---

## 7. Flutter Unit Tests

Test:

- state management
- validation
- formatting
- business calculations
- localization
- entitlement handling
- navigation decisions

---

## 8. Flutter Widget Tests

Every major reusable component must have tests.

Examples:

- buttons
- stepper
- cards
- switches
- chips
- progress ring
- charts
- bottom sheets
- dialogs
- state blocks

Test:

- rendering
- interaction
- disabled state
- loading state
- error state
- accessibility semantics

---

## 9. Admin Tests

Test:

- authentication
- RBAC
- navigation
- CRUD
- search
- filtering
- pagination
- publishing
- confirmation dialogs
- audit events

---

## 10. Authentication Tests

Required cases:

### Registration
- valid
- duplicate email
- invalid email
- weak password

### Login
- valid
- invalid credentials
- locked/rate-limited
- expired session

### Password reset
- valid
- expired token
- reused token

### Session
- refresh
- revoke
- logout
- multiple sessions if supported

---

## 11. Authorization Tests

For every protected endpoint test:

- unauthenticated
- authenticated user
- another user
- correct role
- incorrect role
- correct permission
- missing permission

---

## 12. Workout Tests

Test:

- plan creation
- exercise assignment
- set logging
- progression
- completion
- rest timer
- history
- invalid values
- archived exercise handling

---

## 13. Nutrition Tests

Test:

- calorie calculation
- macro calculation
- meal creation
- plan generation
- food restrictions
- allergies
- invalid values
- daily tracking

---

## 14. AI Tests

AI output must not be tested only with exact text matching.

Test:

- schema validity
- safety constraints
- refusal behavior
- personalization inputs
- context boundaries
- prompt injection resistance
- unavailable model fallback
- timeout behavior

Example:

Expected:

Valid structured recommendation

Not:

Exact paragraph must equal X

---

## 15. AI Evaluation

Maintain a curated evaluation dataset.

Categories:

- Workout
- Nutrition
- Motivation
- Safety
- Personalization
- Adversarial prompts
- Prompt injection

Track:

- pass rate
- safety violations
- hallucination rate
- schema validity

AI changes must be evaluated before production.

---

## 16. Notification Tests

Test:

- permission state
- preference state
- scheduling
- targeting
- delivery failure
- retry
- duplicate prevention
- quiet hours
- localization

---

## 17. Payment Tests

Never use real production payments in automated CI.

Test:

- purchase
- renewal
- cancellation
- expiration
- refund
- webhook
- duplicate webhook
- invalid signature
- entitlement synchronization

Use provider sandbox.

---

## 18. Security Tests

Automate:

- authentication bypass
- authorization bypass
- IDOR
- injection
- rate limits
- malformed payloads
- secret leakage
- dependency vulnerabilities

---

## 19. Regression Testing

Every fixed bug should produce a regression test when practical.

Rule:

```text
Bug fixed
↓
Test added
↓
CI prevents recurrence
```

---

## 20. Database Testing

Test:

- migrations
- rollback strategy where supported
- constraints
- indexes
- foreign keys
- unique constraints
- deletion behavior
- seed/test data

Never depend on production data for tests.

---

## 21. Performance Tests

Test critical APIs:

- authentication
- dashboard
- workout
- nutrition
- AI
- admin search
- content listing

Measure:

- p50
- p95
- p99
- throughput
- error rate

---

## 22. E2E Testing

Critical flows:

### User
```text
Register
↓
Onboarding
↓
Dashboard
↓
Workout
↓
Complete workout
↓
History
```

### Nutrition
```text
Open nutrition
↓
View plan
↓
Log meal
↓
View progress
```

### AI
```text
Open coach
↓
Send question
↓
Receive response
↓
View insight
```

### Subscription
```text
Free
↓
Premium
↓
Entitlement
↓
Premium feature
↓
Cancellation
```

### Admin
```text
Login
↓
Users
↓
Search
↓
Open user
↓
Action
↓
Confirmation
↓
Audit
```

---

## 23. Accessibility Testing

Verify:

- keyboard navigation where applicable
- screen reader labels
- focus behavior
- minimum touch targets
- semantic controls
- chart semantics
- contrast
- RTL navigation

---

## 24. Localization Testing

Test:

- English
- Arabic
- RTL
- long strings
- numbers
- dates
- currency
- empty states
- errors

No hardcoded user-facing strings.

---

## 25. Responsive Testing

### Mobile
- small phone
- standard phone
- large phone
- tablet

### Admin
- desktop
- smaller desktop
- tablet/collapsed navigation

---

## 26. Failure Testing

Test:

- API unavailable
- database unavailable
- Redis unavailable
- AI provider unavailable
- notification provider unavailable
- payment provider unavailable
- network timeout
- malformed external response

Expected behavior must be defined.

---

## 27. CI Quality Gates

Pull request must fail if:

- tests fail
- lint fails
- formatting fails
- type checking fails
- security scan fails at configured severity
- coverage falls below required threshold
- API contract breaks
- build fails

---

## 28. Coverage

Coverage is a signal, not the goal.

Critical business logic must have high coverage.

Prioritize:

- Authentication
- Authorization
- Payments
- Entitlements
- Workout calculations
- Nutrition calculations
- AI safety
- Notifications
- Admin permissions

Avoid writing meaningless tests solely to increase percentage.

---

## 29. Test Data

Test data must be:

- deterministic
- isolated
- reproducible
- non-production
- free of real personal information

Use factories/builders.

Avoid massive static fixture duplication.

---

## 30. Mocking Rules

Mock external systems when unit testing.

Do not mock the entire application.

Integration tests must exercise real:

- database
- API boundaries
- relevant services

External provider APIs can use sandbox/mock environments where appropriate.

---

## 31. Definition of Done

M15 is complete only when:

- Backend tests pass
- Flutter tests pass
- Admin tests pass
- API contract tests pass
- Security tests pass
- AI evaluations pass
- Payment sandbox tests pass
- Notification tests pass
- Critical E2E flows pass
- Accessibility checks pass
- RTL/LTR checks pass
- Performance baseline exists
- CI quality gates are enforced
- No critical known regression remains
- Test documentation exists
