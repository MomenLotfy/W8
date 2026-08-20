# M16 — W8 Staging Environment

Status: Implementation Specification
Priority: Critical
Depends On:
- M0_ENGINEERING_FOUNDATION
- M1_BACKEND_FOUNDATION
- M2_IDENTITY
- M3_CORE_USER_DOMAIN
- M4_CONTENT_MANAGEMENT
- M5_WORKOUT_ENGINE
- M6_NUTRITION_ENGINE
- M7_MOBILE_CORE_UI
- M8_AI_COACH
- M9_PERSONALIZATION
- M10_NOTIFICATIONS
- M11_PAYMENTS
- M12_ADMIN_DASHBOARD
- M13_OBSERVABILITY
- M14_SECURITY_HARDENING
- M15_TESTING_QA

Primary Goal:
Create a production-like staging environment where the complete W8 system can be deployed, tested, monitored, and validated before production release.

---

# 1. OBJECTIVE

Staging must behave as closely as possible to production.

It must contain:

- W8 API
- PostgreSQL
- Redis
- background workers
- scheduler
- mobile integration
- admin dashboard
- object/file storage
- notification infrastructure
- AI integration
- payment sandbox/test environment
- observability
- centralized logs
- metrics
- health checks
- security controls

Staging must never share production databases, secrets, storage buckets, queues, or credentials.

---

# 2. ENVIRONMENT ISOLATION

Required environments:

development
staging
production

Each environment must have:

- separate database
- separate Redis
- separate object storage namespace/bucket
- separate secrets
- separate JWT/signing configuration
- separate API credentials
- separate AI provider credentials
- separate payment credentials
- separate notification credentials
- separate monitoring configuration where appropriate

Production credentials must never be available to staging.

---

# 3. CONFIGURATION

Configuration must be environment-driven.

No environment-specific values may be hardcoded.

Required configuration categories:

APP_ENV
APP_VERSION
DATABASE_URL
REDIS_URL
JWT configuration
CORS configuration
storage configuration
email configuration
push notification configuration
AI provider configuration
payment provider configuration
observability configuration

Secrets must come from the deployment secret-management system.

Never commit secrets.

---

# 4. DATABASE

Staging database must use the same:

- PostgreSQL major version
- schema
- migrations
- extensions
- indexes
- constraints

as production.

Deployment process:

1. provision database
2. configure credentials
3. run migrations
4. validate schema
5. validate indexes
6. validate constraints
7. run seed configuration
8. run smoke tests

No manual schema modifications.

All schema changes must exist as migrations.

---

# 5. CONTENT

Staging must contain controlled test content.

Content must be created through the W8 CMS where possible.

Required test content:

- exercises
- muscle groups
- equipment
- workout templates
- workout plans
- nutrition plans
- meals
- ingredients
- AI knowledge content

Production content must not automatically appear in staging.

---

# 6. TEST USERS

Create controlled test accounts.

Required roles:

USER
PREMIUM_USER
COACH
CONTENT_EDITOR
ADMIN
SUPER_ADMIN

Test accounts must not use real personal information.

Use synthetic data.

---

# 7. PAYMENTS

Use payment sandbox/test mode only.

Never connect staging to production payment processing.

Test:

- subscription purchase
- successful payment
- failed payment
- cancellation
- renewal
- expiration
- webhook handling
- duplicate webhook
- invalid webhook
- entitlement update

---

# 8. AI

Staging AI must use:

- staging credentials
- controlled models/configuration
- usage limits
- cost limits

AI requests must be observable.

Every AI request should record:

- request ID
- user ID when permitted
- feature
- model
- latency
- token usage where available
- result status
- failure reason

Never log private conversation content unless explicitly required and safely redacted.

---

# 9. NOTIFICATIONS

Use staging notification configuration.

Test:

- push delivery
- notification preferences
- disabled notifications
- category-specific notifications
- deep links
- retries
- failures

---

# 10. DEPLOYMENT

Staging deployments must be automated.

Preferred flow:

Pull Request
→ CI
→ merge
→ build
→ test
→ security scan
→ deploy staging
→ smoke tests
→ integration tests
→ staging approval

No manual SSH deployment.

---

# 11. DOCKER

All backend services must have production-like container images.

Requirements:

- non-root containers
- minimal base image
- pinned dependencies
- health checks
- deterministic builds
- no secrets in image
- multi-stage builds where useful

---

# 12. HEALTH CHECKS

Required endpoints:

/health/live
/health/ready

Liveness checks process availability.

Readiness checks:

- database
- Redis
- required dependencies

External services should not necessarily make the entire application unhealthy unless required for core functionality.

---

# 13. SMOKE TESTS

After deployment automatically verify:

- API starts
- database reachable
- Redis reachable
- authentication works
- user can access profile
- content can be retrieved
- workout plan can be opened
- nutrition plan can be opened
- AI endpoint responds
- notification service works
- admin authentication works
- admin dashboard loads

---

# 14. DATA RESET

Staging must support controlled reset.

Reset must:

- remove test data
- preserve schema
- preserve configuration
- reseed required reference data

Production reset operations must be impossible through staging tooling.

---

# 15. OBSERVABILITY

Staging must expose:

- application logs
- error logs
- metrics
- traces where configured
- deployment status
- health status

Dashboards must distinguish staging from production.

---

# 16. SECURITY VALIDATION

Run:

- dependency scanning
- container scanning
- secret scanning
- SAST
- API security tests
- authentication tests
- authorization tests
- rate-limit tests

Critical vulnerabilities block release.

---

# 17. ACCEPTANCE CRITERIA

M16 is complete when:

- staging is independently isolated
- complete W8 stack deploys automatically
- database migrations work from clean state
- test users work
- CMS works
- workout engine works
- nutrition engine works
- AI works
- notifications work
- payments use sandbox
- admin works
- observability works
- security scans pass
- smoke tests pass
- rollback has been tested
