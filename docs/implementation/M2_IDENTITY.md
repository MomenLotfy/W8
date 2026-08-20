# M2 — W8 Identity & Authentication

**Document:** M2_IDENTITY.md  
**Product:** W8  
**Milestone:** M2  
**Status:** Ready for Implementation  
**Priority:** P0 — Critical  
**Depends On:** M0, M1  
**Blocks:** M3–M19  

---

# 1. Purpose

M2 implements the identity, authentication, session, and account-security foundation for W8.

This milestone establishes who a user is and how the system securely manages authenticated sessions.

Authorization and detailed RBAC behavior must follow Section 07 and the account lifecycle requirements of Section 15.

M2 MUST NOT implement workout, nutrition, AI Coach, payments, or other product domains.

---

# 2. Source of Truth

Follow:

1. `/AGENTS.md`
2. `/docs/W8_MASTER_TECHNICAL_SPECIFICATION.md`
3. Section 03 — Domain & Data Model
4. Section 06 — API Contract
5. Section 07 — Security Architecture
6. Section 15 — Authentication, Identity & Account Lifecycle
7. Section 17 — Observability
8. Section 26 — CI/CD & DevSecOps
9. This document

---

# 3. Objectives

Implement:

- User identity
- Account creation
- Authentication
- Password security
- Email verification
- Session lifecycle
- Access tokens
- Refresh tokens according to Section 15
- Logout
- Token revocation
- Password reset
- Account status
- Session management
- Authentication rate limiting
- Brute-force protection
- Security event logging
- Identity-related audit events
- Authentication tests

---

# 4. Non-Goals

M2 MUST NOT implement:

- workout plans
- exercises
- nutrition
- AI Coach
- subscriptions
- payments
- push notifications
- recommendation engine
- production admin features

---

# 5. Identity Model

The implementation MUST distinguish:

```text
Identity
Account
Session
Credential
Role
Permission
Account Status
```

Do not collapse these concepts into one database record if Section 03/07 specifies separate domain concepts.

---

# 6. Account States

The account lifecycle MUST support the states defined in Section 15.

At minimum the architecture must be capable of representing:

- pending verification
- active
- suspended
- disabled
- deleted/deletion pending

Exact states and transitions MUST follow the Master Specification.

Invalid state transitions MUST be rejected.

---

# 7. Task List

## M2-T01 — Identity Domain Model

Create the identity/account domain entities according to Section 03.

Requirements:

- stable identifier
- timestamps
- account status
- unique identity attributes
- lifecycle metadata where required

Do not store unnecessary personal information.

## M2-T02 — Credential Model

Implement secure password credential handling.

Passwords MUST:

- never be stored plaintext
- never be logged
- never be returned by APIs

Use a modern password hashing algorithm appropriate for the backend specification.

Do not implement custom cryptographic algorithms.

## M2-T03 — Registration

Implement registration according to Section 15.

Requirements:

- input validation
- uniqueness handling
- secure password handling
- account creation
- initial status
- verification workflow
- security logging

Registration MUST NOT expose whether a sensitive account identifier already exists if the specification requires enumeration resistance.

## M2-T04 — Email Verification

Implement verification flow.

Requirements:

- single-use verification token
- expiration
- invalidation after successful use
- replay protection
- safe response behavior

Never store raw long-lived verification secrets unnecessarily.

## M2-T05 — Login

Implement authentication.

Requirements:

- credential verification
- account-state verification
- rate limiting
- brute-force mitigation
- session creation
- token issuance
- security logging

Failed authentication responses MUST NOT reveal sensitive account existence information where enumeration protection is required.

## M2-T06 — Access Token

Implement the access-token mechanism defined in Section 15.

Requirements:

- expiration
- issuer/audience validation where applicable
- signature validation
- algorithm allow-list
- minimal claims
- no sensitive data in claims

Never accept an arbitrary algorithm supplied by a token.

## M2-T07 — Refresh Token

Implement refresh-token lifecycle according to Section 15.

Requirements may include:

- rotation
- expiration
- revocation
- reuse detection
- session binding

The exact mechanism MUST follow the Master Specification.

Refresh tokens MUST NOT be exposed to logs.

## M2-T08 — Logout

Implement logout.

Requirements:

- invalidate/revoke the appropriate session/token state
- safe repeated logout
- security event

Logout MUST NOT simply delete a local Flutter token and claim server logout is complete.

## M2-T09 — Session Management

Implement active-session tracking according to Section 15.

Session data should support:

- session ID
- account ID
- created at
- last activity
- expiration
- revocation
- device metadata where allowed

Avoid collecting unnecessary device information.

## M2-T10 — Password Reset Request

Implement password-reset request.

Requirements:

- anti-enumeration behavior
- rate limiting
- expiration
- single-use token
- security event logging

## M2-T11 — Password Reset Completion

Implement password reset completion.

Requirements:

- validate reset token
- securely replace credential
- invalidate relevant sessions according to Section 15
- prevent token reuse
- audit security event

## M2-T12 — Account Status Enforcement

Authentication MUST reject inappropriate account states.

Examples:

- suspended
- disabled
- deleted

The exact behavior must follow Section 15.

## M2-T13 — Authentication Rate Limiting

Protect:

- registration
- login
- verification
- password reset
- refresh

against abuse.

Rate limits must be environment configurable.

Do not implement rate limits only in Flutter.

## M2-T14 — Security Event Logging

Record security-relevant events such as:

- registration
- verification
- login success
- login failure
- logout
- refresh
- refresh reuse detection
- password reset requested
- password reset completed
- account status change

Logs MUST NOT contain passwords or tokens.

## M2-T15 — Authentication Dependencies

Protect authenticated routes through reusable backend dependencies/middleware.

The mechanism must allow future authorization checks without duplicating token parsing logic.

## M2-T16 — Authorization Foundation

M2 establishes the authenticated principal.

It must expose a consistent principal/context object to later authorization logic.

Example conceptual information:

- user_id
- session_id
- role identifiers
- account status

Do not hardcode authorization decisions inside every route.

## M2-T17 — Authentication API Tests

Tests MUST cover:

### Registration

- valid registration
- invalid input
- duplicate handling
- password policy
- verification requirement

### Login

- valid credentials
- invalid credentials
- inactive account
- rate limit
- token issuance

### Refresh

- valid token
- expired token
- revoked token
- reuse detection if required

### Logout

- valid logout
- repeated logout
- revoked session

### Password Reset

- request
- expiration
- reuse prevention
- successful reset
- session invalidation

---

# 8. Password Security

Password policy MUST be defined centrally.

Never:

- log password
- return password
- store plaintext password
- store reversible password encryption

Password hashing parameters must be configurable only through secure deployment configuration where appropriate.

---

# 9. Token Security

Tokens MUST:

- have limited lifetime
- use strong cryptography
- have explicit validation
- not contain secrets
- not be logged
- be revocable according to the chosen architecture

Do not create JWTs with insecure defaults.

---

# 10. Session Security

Sessions must support:

- creation
- validation
- refresh
- revocation
- expiration
- logout

Session invalidation behavior must follow Section 15.

---

# 11. Enumeration Protection

Authentication-related APIs must avoid leaking sensitive account existence information.

Examples include:

- registration
- password reset
- verification
- login

The exact user-facing response must follow the API contract.

---

# 12. Audit Requirements

Security-sensitive actions MUST produce auditable events.

Audit events should contain:

- event type
- actor
- target where applicable
- timestamp
- request ID
- result
- security metadata allowed by policy

Never place credentials or tokens in audit records.

---

# 13. Flutter Integration Requirements

The mobile application must eventually consume the authentication APIs through a dedicated authentication layer.

M2 backend implementation MUST NOT require Flutter to know internal token/session implementation details.

The mobile client should depend on:

- Auth API contract

not backend internals.

---

# 14. Admin Integration Requirements

Admin authentication must use the authentication architecture and authorization model defined by Sections 07 and 15.

Do not create a second authentication system without explicit specification approval.

---

# 15. Security Threats Covered

M2 MUST explicitly test against:

- credential stuffing
- brute force
- token replay
- refresh-token theft/reuse
- session fixation
- account enumeration
- password reset abuse
- verification-token replay
- expired token acceptance
- algorithm confusion
- privilege escalation through identity context

---

# 16. M2 Definition of Done

- [ ] Identity model implemented
- [ ] Account lifecycle implemented
- [ ] Password hashing implemented
- [ ] Registration implemented
- [ ] Email verification implemented
- [ ] Login implemented
- [ ] Access token implemented
- [ ] Refresh token implemented
- [ ] Logout implemented
- [ ] Session lifecycle implemented
- [ ] Password reset implemented
- [ ] Account status enforcement implemented
- [ ] Rate limiting implemented
- [ ] Security events implemented
- [ ] Authentication dependencies implemented
- [ ] Authorization principal implemented
- [ ] Tests pass
- [ ] Security tests pass
- [ ] CI passes
- [ ] No secrets/tokens appear in logs
- [ ] API documentation updated

---

# 17. AI Execution Prompt

```text
You are implementing W8 Milestone M2 — Identity & Authentication.

Before modifying code:

1. Read /AGENTS.md completely.
2. Read the relevant Master Specification sections.
3. Inspect all existing M0 and M1 implementation.
4. Inspect database migrations.
5. Inspect existing API conventions.
6. Identify dependencies and conflicts.

Implement M2 incrementally.

Security is the primary concern.

Never invent authentication behavior where Section 15 defines it.

Never implement custom cryptography.

Never log credentials, passwords, access tokens, refresh tokens,
verification tokens, or password-reset tokens.

Never weaken authentication to make tests pass.

Never implement authorization by checking UI state.

All authentication security decisions must be enforced server-side.

For each task:

- implement
- test
- format
- lint
- type-check
- security-check
- inspect diff

Before declaring M2 complete, run the full authentication test suite
and verify all M2 Definition of Done requirements.

If a security-sensitive requirement is ambiguous, STOP and report it.
Do not guess.
```

---

# 18. Milestone Exit Gate

M3 MUST NOT begin until:

- All M2 tests pass

AND

- Security tests pass

AND

- CI passes

AND

- Authentication APIs conform to Section 06

AND

- Identity model conforms to Section 03

AND

- Lifecycle conforms to Section 15

AND

- Security controls conform to Section 07

AND

- No credentials/tokens are exposed

AND

- M2 Definition of Done is complete
