
# M1 — W8 Backend Foundation

**Document:** M1_BACKEND_FOUNDATION.md  
**Product:** W8  
**Milestone:** M1  
**Status:** Ready for Implementation  
**Priority:** P0 — Blocking  
**Depends On:** M0  
**Blocks:** M2–M19  

---

# 1. Purpose

M1 establishes the production-oriented backend foundation on top of the engineering infrastructure created in M0.

This milestone creates the architectural backend layers, database conventions, API conventions, error model, request lifecycle, dependency injection, health/readiness mechanisms, and foundational infrastructure required by later W8 features.

M1 MUST NOT implement user authentication or business-domain features.

---

# 2. Source of Truth

Follow:

1. `/AGENTS.md`
2. `/docs/W8_MASTER_TECHNICAL_SPECIFICATION.md`
3. Section 05 — System Architecture
4. Section 06 — API Contract
5. Section 07 — Security Architecture
6. Section 09 — Backend Structure & Coding Standards
7. Section 17 — Observability
8. Section 26 — CI/CD & DevSecOps
9. This document

---

# 3. Objectives

M1 establishes:

- Backend architecture
- Application startup lifecycle
- Dependency injection
- Database session management
- Transaction boundaries
- Repository conventions
- Service/use-case conventions
- API routing conventions
- API versioning
- Request validation
- Response serialization
- Error handling
- Exception mapping
- Health checks
- Readiness checks
- Database connectivity
- Redis connectivity
- CORS policy
- Request correlation
- Base logging
- API documentation
- Pagination foundation
- Rate-limit integration point
- Security middleware foundation
- Test architecture

---

# 4. Non-Goals

M1 MUST NOT implement:

- User registration
- Login
- Password reset
- OAuth
- User profiles
- Workouts
- Nutrition
- AI Coach
- Payments
- Notifications
- Admin business operations

---

# 5. Backend Architectural Layers

The backend MUST maintain clear boundaries:

```text
API / HTTP
    ↓
Application / Use Cases
    ↓
Domain
    ↓
Infrastructure
```

Infrastructure dependencies MUST NOT leak into domain logic.

---

# 6. Recommended Structure

The final backend should follow the architecture defined in Section 09.

A representative structure:

```text
apps/api/
├── app/
│   ├── main.py
│   │
│   ├── api/
│   │   ├── dependencies/
│   │   ├── routes/
│   │   └── schemas/
│   │
│   ├── application/
│   │   ├── services/
│   │   └── use_cases/
│   │
│   ├── domain/
│   │   ├── entities/
│   │   ├── value_objects/
│   │   ├── exceptions/
│   │   └── interfaces/
│   │
│   ├── infrastructure/
│   │   ├── database/
│   │   ├── redis/
│   │   ├── repositories/
│   │   └── external/
│   │
│   ├── core/
│   │   ├── config.py
│   │   ├── logging.py
│   │   ├── errors.py
│   │   └── security.py
│   │
│   └── tests/
│
├── alembic/
└── pyproject.toml
```

The exact structure may follow Section 09 if it differs.

---

# 7. Task List

## M1-T01 — Application Lifecycle

Implement startup/shutdown lifecycle.

Requirements:

- configuration loading
- logging initialization
- database initialization
- Redis initialization
- graceful shutdown
- resource cleanup

Startup MUST fail clearly when a mandatory dependency is unavailable.

## M1-T02 — Configuration

Create strongly typed configuration.

Requirements:

- App settings
- Database settings
- Redis settings
- CORS settings
- Logging settings
- Environment
- API settings
- Security settings

Use environment variables.

Never expose secret values in logs.

## M1-T03 — Database Session Management

Implement:

- async database engine where specified
- session factory
- request-scoped session
- transaction boundaries
- rollback on failure
- connection cleanup

Database sessions MUST NOT leak.

## M1-T04 — Migration Architecture

Implement Alembic conventions.

Requirements:

- migration directory
- naming conventions
- migration review process
- upgrade
- downgrade
- CI validation

Destructive migrations require explicit review.

## M1-T05 — Repository Pattern

Define repository interfaces where required by the architecture.

Repositories MUST:

- isolate persistence
- avoid business logic
- expose domain-appropriate operations
- support test doubles

Do not create repositories purely for every trivial query if the architecture does not benefit from them.

## M1-T06 — Application Service Pattern

Define use-case/service conventions.

Application services own orchestration.

They MUST NOT contain raw HTTP concerns.

## M1-T07 — API Router Structure

Implement versioned API structure.

Example:

```text
/api/v1/...
```

The exact paths MUST follow Section 06.

## M1-T08 — Request Validation

All external input MUST be validated.

Validation must cover:

- type
- required fields
- length
- range
- format
- enum values
- nested structures

Validation failures MUST use the standard API error format.

## M1-T09 — Error Model

Create a unified error contract.

At minimum distinguish:

- Validation error
- Authentication error
- Authorization error
- Not found
- Conflict
- Business rule violation
- Rate limit
- Dependency failure
- Internal server error

Internal stack traces MUST NOT be returned to clients.

## M1-T10 — Exception Handling

Create centralized exception handlers.

Requirements:

- consistent JSON structure
- correlation/request ID
- safe messages
- server-side logging
- correct HTTP status codes

## M1-T11 — Health & Readiness

Implement separate concepts:

- /health
- /readiness

Health answers whether the application process is alive.

Readiness verifies required dependencies.

Do not expose sensitive infrastructure information.

## M1-T12 — Redis Abstraction

Implement Redis access abstraction.

Requirements:

- connection lifecycle
- health verification
- timeout handling
- failure handling

Redis outages MUST produce controlled behavior.

## M1-T13 — CORS

Implement environment-aware CORS.

Development may be permissive within reason.

Production MUST use explicit trusted origins.

Never use unrestricted wildcard CORS in production for authenticated APIs unless explicitly justified and documented.

## M1-T14 — Request Correlation

Every request should receive or propagate a correlation/request ID.

The ID must appear in:

- logs
- errors where appropriate
- observability context

Do not trust client-supplied IDs blindly without validation.

## M1-T15 — Logging Foundation

Implement structured logs.

Required baseline fields:

- timestamp
- level
- service
- environment
- request_id
- event

Sensitive information MUST NOT be logged.

## M1-T16 — API Documentation

OpenAPI documentation must reflect actual API behavior.

Do not document endpoints that do not exist.

Do not create fake endpoints for documentation completeness.

## M1-T17 — Pagination Foundation

Define reusable pagination conventions.

Requirements:

- page/limit or cursor strategy according to Section 06
- maximum page size
- validation
- predictable ordering

Unbounded production collection endpoints are prohibited.

## M1-T18 — Security Middleware Foundation

Prepare infrastructure for:

- security headers where applicable
- request size limits
- rate limiting integration
- trusted host configuration
- secure CORS
- safe error handling

Authentication enforcement belongs to M2.

## M1-T19 — Test Architecture

Establish:

- unit tests
- integration tests
- API tests
- database tests

Use isolated test configuration.

Tests MUST NOT use production infrastructure.

## M1-T20 — Backend CI

CI must execute:

- format check
- lint
- type checking
- unit tests
- integration tests
- migration validation
- dependency/security checks

---

# 8. Database Rules

M1 establishes the rules used by all later migrations.

Required principles:

- explicit primary keys
- foreign keys
- appropriate indexes
- unique constraints
- NOT NULL where semantically required
- timestamps where required
- explicit cascade behavior
- no accidental orphan records

Business-domain tables belong to later milestones.

---

# 9. API Rules

Every endpoint implemented after M1 must have:

- Authentication requirement
- Authorization requirement
- Request schema
- Response schema
- Error behavior
- Validation rules
- Pagination behavior if applicable
- Rate-limit classification if applicable
- Tests

---

# 10. Security Requirements

M1 MUST prevent:

- secret leakage
- uncontrolled CORS
- unsafe exception exposure
- unbounded payloads
- unbounded queries
- database connection leaks
- Redis connection leaks
- unrestricted pagination
- insecure production defaults

---

# 11. Performance Requirements

Backend foundation must:

- use connection pooling appropriately
- avoid opening a new connection per operation
- use async I/O where required
- avoid blocking calls in async request paths
- enforce query limits
- configure sensible timeouts

---

# 12. M1 Definition of Done

- [ ] Backend architecture implemented
- [ ] Configuration implemented
- [ ] Database session management implemented
- [ ] Alembic configured
- [ ] Redis abstraction implemented
- [ ] API routing implemented
- [ ] Error model implemented
- [ ] Validation implemented
- [ ] Health implemented
- [ ] Readiness implemented
- [ ] CORS configured
- [ ] Request correlation implemented
- [ ] Structured logging implemented
- [ ] Pagination foundation implemented
- [ ] Security middleware foundation implemented
- [ ] Tests passing
- [ ] CI passing
- [ ] No secrets committed
- [ ] Documentation updated

---

# 13. AI Execution Prompt

```text
You are implementing W8 Milestone M1.

Read:

1. /AGENTS.md
2. /docs/W8_MASTER_TECHNICAL_SPECIFICATION.md
3. Section 05
4. Section 06
5. Section 07
6. Section 09
7. Section 17
8. Section 26
9. This milestone document

Inspect the existing M0 implementation before changing anything.

Implement only M1.

Do not implement authentication or business features.

Preserve the architecture boundaries.

For every implementation:

- validate external input
- handle errors safely
- avoid leaking secrets
- add tests
- update documentation where needed
- run formatting
- run linting
- run type checking
- run tests
- inspect the final diff

Never invent API contracts.

Never create fake production behavior.

If the specification is ambiguous, stop and report the ambiguity.

The implementation is complete only when all M1 acceptance criteria pass.
```

---

# 14. Milestone Exit Gate

M2 MUST NOT begin until:

- M1 tests pass

AND

- CI passes

AND

- database migrations work

AND

- health/readiness work

AND

- security baseline passes

AND

- API conventions are documented

AND

- M1 Definition of Done is complete
