# M18 — W8 Production Release

Status: Implementation Specification
Priority: Critical
Depends On:
- M17_PRODUCTION_READINESS

Primary Goal:
Release W8 safely to production using controlled, observable, reversible deployment procedures.

---

# 1. RELEASE PRINCIPLES

Production release must be:

- automated
- auditable
- observable
- reversible
- incremental where possible
- protected by health checks

Never perform undocumented manual production modifications.

---

# 2. RELEASE ARTIFACT

Every release must have an immutable version.

Recommended:

Semantic Versioning

MAJOR.MINOR.PATCH

Example:

v1.0.0

The version must map to:

- Git commit
- container image
- mobile build
- release notes
- deployment record

---

# 3. RELEASE CANDIDATE

Create release candidate from a protected branch.

Required:

- CI passed
- tests passed
- security scans passed
- staging passed
- changelog generated
- version assigned

---

# 4. DATABASE MIGRATION

Before application deployment:

1. backup database
2. validate migration
3. run migration if required
4. verify schema
5. deploy compatible application

Prefer:

expand
→ migrate
→ contract

rather than destructive one-step migrations.

---

# 5. BACKEND DEPLOYMENT

Deployment sequence:

1. deploy new image
2. start new instances
3. run health checks
4. verify readiness
5. route traffic
6. monitor
7. retire old version

If health checks fail:

stop rollout
→ investigate
→ rollback

---

# 6. ZERO/MINIMAL DOWNTIME

Production deployment should maintain service availability.

Use:

- rolling deployment
- blue/green
- canary

depending on infrastructure.

---

# 7. MOBILE RELEASE

Android and iOS releases must be built from tagged commits.

Verify:

- version
- build number
- environment configuration
- API endpoint
- analytics configuration
- crash reporting
- push configuration
- signing
- privacy configuration

Never ship staging credentials.

---

# 8. ADMIN RELEASE

Admin must be deployed with the same version compatibility requirements as the API.

Verify:

- authentication
- RBAC
- API compatibility
- content management
- audit logs

---

# 9. POST-DEPLOYMENT SMOKE TEST

Immediately test:

Authentication
Profile
Dashboard
Workout
Nutrition
AI Coach
Notifications
Subscription
Admin login
CMS

---

# 10. MONITORING WINDOW

After release monitor:

- error rate
- latency
- traffic
- CPU
- memory
- database
- Redis
- AI errors
- payment errors
- notification errors

Critical anomalies trigger rollback assessment.

---

# 11. RELEASE ROLLBACK

Rollback triggers include:

- severe outage
- elevated 5xx
- authentication failure
- payment corruption
- data corruption
- security vulnerability
- severe performance regression

Rollback must be documented.

---

# 12. FEATURE FLAGS

High-risk features should support controlled activation.

Examples:

AI Coach
Premium features
new recommendation engine
new workout algorithm
new notification campaigns

Feature flags must support:

enable
disable
percentage rollout
role/environment targeting where appropriate

---

# 13. RELEASE NOTES

Every release must document:

- new features
- improvements
- bug fixes
- security changes
- database changes
- known limitations
- rollback considerations

---

# 14. AUDITABILITY

Record:

- who approved
- who deployed
- what version
- when deployed
- environment
- commit SHA
- deployment result
- rollback if performed

---

# 15. ACCEPTANCE CRITERIA

M18 is complete when:

- production deployment succeeds
- smoke tests pass
- monitoring is healthy
- mobile release artifacts are valid
- admin works
- payments work
- notifications work
- rollback procedure has been validated
- release is documented
