# M0 — W8 Engineering Foundation

**Document:** M0_ENGINEERING_FOUNDATION.md  
**Product:** W8  
**Milestone:** M0  
**Status:** Ready for Implementation  
**Priority:** P0 — Blocking  
**Depends On:** None  
**Blocks:** M1–M19  

---

# 1. Purpose

Milestone 0 establishes the engineering foundation for the W8 platform.

This milestone MUST NOT implement product business features.

Its purpose is to create a clean, secure, reproducible, testable, and CI-ready development environment that all future W8 milestones will build upon.

The result must be a repository that can be cloned by a new developer or AI coding agent and brought to a working development state using documented commands.

---

# 2. Source of Truth

The implementation MUST follow:

1. `/AGENTS.md`
2. `/docs/W8_MASTER_TECHNICAL_SPECIFICATION.md`
3. Relevant sections referenced by each task
4. This milestone document
5. Existing repository code

If two sources conflict, follow the authority hierarchy defined in `AGENTS.md`.

The HTML prototype under:

`/docs/design/index.html`

is a visual/interaction reference only.

It MUST NOT be treated as production architecture.

---

# 3. Milestone Objectives

M0 must establish:

- Git repository conventions
- Monorepo structure
- Backend project foundation
- Flutter application foundation
- Admin application foundation
- Shared package structure
- Environment configuration strategy
- Local development infrastructure
- Docker foundation
- Database development foundation
- Redis development foundation
- Code quality tooling
- Formatting/linting
- Basic automated tests
- CI baseline
- Security baseline
- Documentation baseline
- Development scripts
- Health-check foundation

---

# 4. Explicit Non-Goals

M0 MUST NOT implement:

- Authentication
- User registration
- Workout functionality
- Nutrition functionality
- AI Coach
- Payments
- Push notifications
- Subscription logic
- Production deployment
- Production infrastructure
- Admin business functionality
- Real exercise data
- Real nutrition data
- Real workout plans

Placeholder/test fixtures are allowed only for infrastructure verification and automated tests.

---

# 5. Target Repository Structure

The repository MUST evolve toward:

```text
w8/
├── AGENTS.md
├── README.md
├── LICENSE
├── .gitignore
├── .env.example
│
├── docs/
│   ├── W8_MASTER_TECHNICAL_SPECIFICATION.md
│   ├── audit/
│   ├── design/
│   └── implementation/
│
├── apps/
│   ├── api/
│   ├── mobile/
│   └── admin/
│
├── packages/
│
├── infrastructure/
│   ├── docker/
│   ├── database/
│   └── local/
│
├── scripts/
│
├── tests/
│
└── .github/
    └── workflows/
```

M0 may create additional directories when required by the selected technologies, but unnecessary structural complexity MUST be avoided.

---

# 6. Technology Baseline

Unless the Master Specification explicitly overrides a technology:

## Backend

- Python
- FastAPI
- Pydantic
- SQLAlchemy
- Alembic
- PostgreSQL
- Redis
- pytest

## Mobile

- Flutter
- Dart
- Material/Cupertino primitives as appropriate
- Flutter localization infrastructure

## Admin

The admin application MUST follow the architecture specified in Section 11.

The implementation MUST NOT introduce a second unrelated architecture merely for convenience.

## Infrastructure

- Docker
- Docker Compose for local development
- GitHub Actions for CI

---

# 7. Task Execution Rules

Every task in this milestone MUST follow:

```text
Read specification
    ↓
Inspect repository
    ↓
Identify dependencies
    ↓
Create implementation plan
    ↓
Implement smallest correct change
    ↓
Run formatting
    ↓
Run static analysis
    ↓
Run tests
    ↓
Inspect diff
    ↓
Update documentation
    ↓
Verify acceptance criteria
```

AI MUST NOT skip repository inspection.

AI MUST NOT assume the repository is empty or matches an expected state.

---

# 8. Task IDs

Tasks use:

- M0-T01
- M0-T02
- M0-T03
- ...

A task is not complete until its acceptance criteria are satisfied.

---

# 9. M0 Task List

## M0-T01 — Repository Initialization

### Objective

Initialize the W8 Git repository and baseline metadata.

### Requirements

Create:

- README.md
- LICENSE
- .gitignore
- .env.example
- AGENTS.md

Create the required directory structure.

### Acceptance Criteria

- Repository initializes successfully.
- No secrets are committed.
- .env.example contains placeholders only.
- README contains local setup instructions.
- AGENTS.md is present at repository root.
- Git status is clean after commit.

## M0-T02 — Monorepo Structure

### Objective

Create the application and shared-package boundaries.

### Requirements

Create:

- apps/api
- apps/mobile
- apps/admin
- packages
- infrastructure
- scripts
- tests

Each application MUST have a clear ownership boundary.

### Acceptance Criteria

- Backend code cannot import Flutter code.
- Flutter code cannot directly access backend internals.
- Shared packages have documented ownership.
- No circular application dependency exists.

## M0-T03 — Backend Bootstrap

### Objective

Create the minimal FastAPI backend.

### Requirements

Implement:

- GET /health

The endpoint MUST return a structured response.

Example:

```json
{
  "status": "ok"
}
```

The exact production response MUST follow Section 06.

### Acceptance Criteria

- API starts successfully.
- /health responds successfully.
- Invalid requests produce structured errors.
- Tests exist for the health endpoint.
- No business logic is introduced.

## M0-T04 — Flutter Bootstrap

### Objective

Create the W8 mobile application foundation.

### Requirements

- Flutter application starts.
- W8 branding is used.
- Theme foundation exists.
- Localization foundation exists.
- Light and dark theme foundations exist.
- No prototype HTML is embedded as production UI.

### Acceptance Criteria

- App launches.
- No analyzer errors.
- No hardcoded secrets.
- Theme switching foundation works.
- English and Arabic locales can be initialized.

## M0-T05 — Admin Bootstrap

### Objective

Create the admin application foundation.

### Requirements

- Application starts.
- Responsive shell foundation exists.
- Theme foundation exists.
- Localization foundation exists.
- Authentication UI may be represented structurally but MUST NOT pretend authentication is functional.

### Acceptance Criteria

- Admin application builds.
- No fake authenticated state is presented as production behavior.
- No business data is hardcoded.

## M0-T06 — Local Infrastructure

### Objective

Create reproducible local infrastructure.

### Services

At minimum:

- PostgreSQL
- Redis
- API

Additional services MUST only be added when justified.

### Requirements

- Docker Compose configuration.
- Persistent development volumes.
- Health checks.
- Environment-based configuration.
- No production secrets.

### Acceptance Criteria

A new developer can run the documented command and start the required local services.

## M0-T07 — Configuration System

### Objective

Create centralized environment configuration.

### Requirements

Configuration MUST distinguish:

- development
- test
- staging
- production

Secrets MUST come from environment/secret-management mechanisms.

### Forbidden

- API keys in source code
- passwords in source code
- JWT secrets in source code
- database passwords in source code

## M0-T08 — Database Foundation

### Objective

Prepare PostgreSQL and migration infrastructure.

### Requirements

- SQLAlchemy initialization.
- Alembic initialization.
- Database connection configuration.
- Migration command documentation.
- Connection health check.

No business tables are required unless explicitly required by the architecture.

### Acceptance Criteria

- Database connects.
- Migration tooling works.
- A migration can be generated/applied/rolled back in development.
- Database credentials are not hardcoded.

## M0-T09 — Redis Foundation

### Objective

Establish Redis connectivity.

### Requirements

- Redis configuration.
- Connection abstraction.
- Health check.
- Test configuration.

Redis MUST NOT become a mandatory dependency for functionality that does not require it.

## M0-T10 — Code Quality

### Backend

Configure:

- Formatter
- Linter
- Type checking
- pytest

### Flutter

Configure:

- dart format
- flutter analyze
- Flutter tests

### Admin

Configure the formatter/linter appropriate to the selected stack.

### Acceptance Criteria

CI and local commands use the same quality gates.

## M0-T11 — Testing Foundation

Create:

```text
tests/
├── backend/
├── mobile/
└── admin/
```

Additional test organization may exist inside each application.

At minimum:

- API health test
- configuration test
- basic Flutter widget test
- admin bootstrap test where supported

## M0-T12 — CI Foundation

Create GitHub Actions workflows.

Minimum checks:

```text
Backend:
- install dependencies
- lint
- type check
- tests

Mobile:
- dependency resolution
- format check
- analyze
- tests

Admin:
- install dependencies
- lint
- tests/build
```

CI MUST fail on blocking errors.

## M0-T13 — Security Baseline

Implement baseline controls:

- .gitignore secret protection
- environment configuration
- dependency lock files
- basic dependency scanning
- no secrets in logs
- safe error handling
- secure default configuration

No security feature may be represented as complete unless it actually works.

## M0-T14 — Development Scripts

Create scripts for common operations.

Examples:

- scripts/dev.*
- scripts/test.*
- scripts/lint.*
- scripts/format.*
- scripts/db.*

Commands MUST be documented.

## M0-T15 — Documentation

Update:

README.md

with:

- prerequisites
- installation
- local environment
- environment variables
- starting services
- running tests
- linting
- formatting
- database commands
- troubleshooting

---

# 10. M0 Security Requirements

M0 MUST establish:

- no committed credentials
- no default production passwords
- no debug secrets
- safe exception responses
- isolated test configuration
- secure environment handling
- dependency lock/verification
- CI security checks

---

# 11. M0 Testing Requirements

Every infrastructure component must have at least one verification path.

The final M0 test matrix must cover:

| Area | Required |
|---|---|
| API startup | Yes |
| API health | Yes |
| Database connection | Yes |
| Redis connection | Yes |
| Flutter startup | Yes |
| Flutter analyzer | Yes |
| Admin startup/build | Yes |
| CI | Yes |
| Configuration | Yes |

---

# 12. M0 Definition of Done

M0 is complete only when:

- [ ] Repository structure exists
- [ ] AGENTS.md exists
- [ ] Master specification exists
- [ ] Prototype reference exists
- [ ] Audit reference exists
- [ ] API starts
- [ ] Flutter app starts
- [ ] Admin starts
- [ ] PostgreSQL starts
- [ ] Redis starts
- [ ] Configuration works
- [ ] Tests pass
- [ ] Lint passes
- [ ] Formatting passes
- [ ] CI passes
- [ ] No secrets are committed
- [ ] Documentation is complete
- [ ] Git working tree is clean

---

# 13. AI Execution Prompt

```text
You are implementing W8 Milestone M0.

Before modifying any file:

1. Read /AGENTS.md completely.
2. Read the relevant sections of /docs/W8_MASTER_TECHNICAL_SPECIFICATION.md.
3. Inspect the repository.
4. Inspect existing configuration and tooling.
5. Identify conflicts or missing requirements.

Implement only the requested M0 task.

Do not implement future product features.

Do not introduce mock production functionality.

Do not refactor unrelated code.

After implementation:

1. Format code.
2. Run static analysis.
3. Run tests.
4. Run security checks available for the task.
5. Inspect git diff.
6. Verify acceptance criteria.
7. Report files changed.
8. Report commands executed.
9. Report test results.
10. Report unresolved issues.

If a requirement is ambiguous or conflicts with the Master Specification, stop and report the ambiguity instead of inventing a behavior.
```

---

# 14. Milestone Exit Gate

M0 may transition to M1 only after all M0 acceptance criteria pass.

No downstream milestone may compensate for an incomplete M0 foundation.
