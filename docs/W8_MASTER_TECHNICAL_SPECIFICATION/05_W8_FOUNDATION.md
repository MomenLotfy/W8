تمام. هنكمل بنفس مستوى الـMaster Specification، وبصياغة جاهزة للنسخ مباشرة داخل W8_MASTER_TECHNICAL_SPECIFICATION.md.
هنا هنثبت 5 Sections مهمين جدا لأنهم من أكتر الأجزاء اللي ممكن تسبب مشاكل مستقبلية لو الـAI نفذها بشكل سريع: Payments, Observability, Testing, CI/CD/DevSecOps, Performance/Cost.
16 — W8 Payments, Subscriptions & Entitlements
16.1 Purpose
The W8 payment system manages:
Premium subscriptions.
Subscription lifecycle.
Entitlements.
Trials.
Renewals.
Cancellations.
Grace periods.
Payment failures.
Refunds.
Store purchases.
Subscription restoration.
Server-side entitlement validation.
The payment architecture must treat external payment providers as the source of truth for payment transactions while W8 remains the source of truth for application entitlements.
16.2 Core Principle
Payment status and application access are separate concepts.
Payment Provider
      │
      ▼
Transaction Validation
      │
      ▼
Subscription State
      │
      ▼
Entitlement Engine
      │
      ▼
W8 Feature Access
The mobile application must never decide independently whether a user is Premium.
16.3 Payment Architecture
Flutter Mobile
      │
      ▼
W8 API
      │
      ├── Subscription Service
      ├── Entitlement Service
      ├── Payment Verification
      └── Webhook Processing
             │
             ├── App Store
             ├── Google Play
             └── Future Payment Providers
The architecture must support adding additional payment providers without rewriting business logic.
16.4 Provider Abstraction
Use a provider abstraction:
PaymentProvider
 ├── verifyPurchase()
 ├── getSubscriptionStatus()
 ├── acknowledgePurchase()
 ├── restorePurchases()
 └── handleWebhook()
Business logic must not depend directly on Apple/Google SDK implementation details.
16.5 Subscription Products
Subscriptions must have stable internal identifiers.
Example:
W8_PREMIUM_MONTHLY
W8_PREMIUM_YEARLY
Provider-specific identifiers must be mapped internally.
Example:
Internal Product
       │
       ├── Apple Product ID
       └── Google Product ID
Never use store product IDs as the primary domain identifier.
16.6 Subscription States
Canonical W8 subscription states:
NONE
TRIALING
ACTIVE
GRACE_PERIOD
PAST_DUE
CANCELLED
EXPIRED
REVOKED
REFUNDED
Provider-specific states must be normalized into these internal states.
16.7 Subscription Lifecycle
Typical lifecycle:
NONE
 ↓
TRIALING
 ↓
ACTIVE
 ↓
RENEWED
 ↓
ACTIVE
Cancellation:
ACTIVE
 ↓
CANCELLED
 ↓
EXPIRED
Payment failure:
ACTIVE
 ↓
PAST_DUE
 ↓
GRACE_PERIOD
 ↓
EXPIRED
Refund/revocation:
ACTIVE
 ↓
REFUNDED / REVOKED
16.8 Entitlements
Entitlements represent what the user is allowed to access.
Example:
premium_access
advanced_ai_coach
premium_workout_plans
advanced_insights
premium_nutrition
Entitlements must be independent from UI.
Example:
Subscription
     ↓
Entitlement
     ↓
Feature Authorization
The UI only displays the result of entitlement evaluation.
16.9 Entitlement Model
Conceptually:
User
 │
 ├── Subscription
 │       └── Product
 │
 └── Entitlements
         ├── premium_access
         ├── ai_advanced
         └── advanced_insights
The backend must calculate the authoritative entitlement state.
16.10 Entitlement Enforcement
Premium APIs must verify entitlements server-side.
Incorrect:
Flutter:
if (isPremium) {
   callPremiumAPI();
}
Correct:
Flutter
   ↓
Premium API
   ↓
Authentication
   ↓
Entitlement Check
   ↓
Allow / Deny
Flutter checks are UX optimization only.
16.11 Purchase Flow
User selects plan
      ↓
Store purchase UI
      ↓
Purchase completed
      ↓
Receipt / transaction obtained
      ↓
W8 backend verification
      ↓
Transaction persisted
      ↓
Subscription updated
      ↓
Entitlement recalculated
      ↓
Mobile receives updated state
The application must not grant permanent access based solely on a successful client-side purchase callback.
16.12 Server Verification
Purchase verification must occur server-side.
The backend validates:
Product.
Transaction identifier.
User association.
Purchase state.
Expiration.
Cancellation/revocation.
Provider authenticity.
Duplicate transaction processing must be prevented.
16.13 Idempotency
Payment operations must be idempotent.
A transaction received multiple times must produce the same final state.
Example:
Webhook #1 → transaction_123
Webhook #2 → transaction_123
Webhook #3 → transaction_123
Result:
ONE subscription state transition
ONE logical transaction
Use unique provider transaction identifiers and idempotency keys.
16.14 Webhooks
Provider webhooks must be treated as asynchronous authoritative updates.
Flow:
Provider
   ↓
Webhook Endpoint
   ↓
Signature Verification
   ↓
Schema Validation
   ↓
Idempotency Check
   ↓
Queue
   ↓
Payment Worker
   ↓
Subscription Update
   ↓
Entitlement Recalculation
Webhook handlers must be fast and must not perform long-running operations synchronously.
16.15 Webhook Security
Webhook endpoints must validate provider authenticity.
Requirements:
Signature verification.
Timestamp validation where supported.
Replay protection.
Schema validation.
Idempotency.
Rate limiting where appropriate.
No trust based on request body alone.
Invalid webhook requests must be rejected.
16.16 Subscription Restoration
Users must be able to restore purchases after:
Reinstall.
Device change.
Logout/login.
App migration.
Restore flow:
Store
 ↓
Current Purchases
 ↓
W8 Backend
 ↓
Verification
 ↓
User Association
 ↓
Entitlement Recalculation
16.17 Cancellation
Cancellation generally means:
Do not renew
It does not necessarily mean:
Immediately remove premium access
The system must respect provider-defined expiration dates unless access has explicitly been revoked.
16.18 Grace Period
When supported:
ACTIVE
 ↓
PAYMENT_FAILURE
 ↓
GRACE_PERIOD
During grace period:
Premium access may remain active.
User should be notified.
Billing state should be visible.
The system should continue monitoring provider state.
16.19 Refunds
Refund events must trigger backend reconciliation.
Possible result:
REFUNDED
 ↓
Entitlement Revoked
The exact policy must be centralized rather than implemented in Flutter.
16.20 Subscription Database Principles
The system should distinguish:
Products
Subscriptions
Transactions
Entitlements
Provider Events
Do not combine them into one generic table.
16.21 Subscription Audit
Record important transitions:
subscription_created
subscription_renewed
subscription_cancelled
subscription_expired
subscription_refunded
subscription_revoked
entitlement_granted
entitlement_revoked
16.22 Payment Security Rules
Never store:
Card numbers.
CVV.
Raw payment credentials.
Store authentication credentials.
W8 should store only provider references and required transaction metadata.
16.23 Payment Failure UX
The application must distinguish:
Purchase failed
Payment pending
Payment declined
Subscription expired
Subscription cancelled
Provider unavailable
Restore failed
Do not show raw provider errors.
17 — W8 Observability, Logging, Monitoring & Incident Response
17.1 Purpose
Observability enables W8 to understand:
What is happening.
Why it is happening.
Who is affected.
When it started.
How severe it is.
Whether a deployment caused it.
How to recover.
Observability must be designed from the beginning, not added after production launch.
17.2 Observability Pillars
W8 uses:
Logs
Metrics
Traces
Events
Audit Records
Alerts
Health Checks
17.3 Architecture
Applications
 ├── Flutter
 ├── API
 ├── Workers
 ├── Admin
 └── Infrastructure
        │
        ▼
Telemetry Collection
        │
        ├── Logs
        ├── Metrics
        └── Traces
        │
        ▼
Observability Platform
        │
        ├── Dashboards
        ├── Alerts
        └── Incident Response
17.4 Structured Logging
All backend logs must be structured.
Example:
{
  "timestamp": "2026-08-20T12:00:00Z",
  "level": "ERROR",
  "service": "api",
  "event": "subscription_verification_failed",
  "request_id": "req_123",
  "user_id": "usr_123",
  "provider": "store",
  "error_code": "INVALID_TRANSACTION"
}
Logs must not contain secrets.
17.5 Request Correlation
Every request should receive a request ID.
Client
 ↓
request_id
 ↓
API
 ↓
Service
 ↓
Database / Queue / Worker
The same correlation ID should be propagated across asynchronous processing where possible.
17.6 Log Levels
Use:
DEBUG
INFO
WARNING
ERROR
CRITICAL
Production DEBUG logging must be disabled or tightly controlled.
17.7 Sensitive Data Rules
Never log:
password
access_token
refresh_token
API keys
payment credentials
OTP
private secrets
full sensitive user payloads
User identifiers should be minimized or pseudonymized where appropriate.
17.8 Metrics
Core metrics include:
API
request_count
request_latency
error_rate
5xx_rate
4xx_rate
Database
connection_pool_usage
query_latency
slow_queries
connection_errors
Redis
memory_usage
hit_rate
latency
evictions
Workers
queue_depth
job_duration
job_failures
retry_count
AI
request_count
token_usage
latency
failure_rate
estimated_cost
Payments
purchase_success_rate
verification_failures
webhook_failures
subscription_state_changes
17.9 Golden Signals
Monitor:
Latency
Traffic
Errors
Saturation
These should be available for the primary API and critical infrastructure.
17.10 Distributed Tracing
Tracing should cover important flows:
API request
 ↓
Service
 ↓
Database
 ↓
Redis
 ↓
AI provider
Trace propagation should use standard correlation mechanisms.
17.11 Health Checks
Provide:
/health/live
/health/ready
Liveness
Answers:
Is the process alive?
Readiness
Answers:
Can this instance safely receive traffic?
Readiness may check critical dependencies.
17.12 Dependency Health
Monitor:
PostgreSQL.
Redis.
Queue.
Object storage.
AI provider.
Payment providers.
Push provider.
A dependency failure must not necessarily make the entire API unavailable.
17.13 Alerting
Alerts must be actionable.
Bad alert:
Something failed.
Good:
API 5xx rate > 5% for 5 minutes
Examples:
High API error rate
High latency
Database saturation
Queue backlog
Payment verification failures
AI provider failure
Notification delivery degradation
Storage failure
17.14 Alert Severity
SEV-1 Critical
SEV-2 High
SEV-3 Medium
SEV-4 Low
Severity must reflect user/business impact.
17.15 Incident Response
Incident lifecycle:
Detect
 ↓
Classify
 ↓
Acknowledge
 ↓
Contain
 ↓
Investigate
 ↓
Recover
 ↓
Verify
 ↓
Postmortem
17.16 Incident Runbooks
Critical systems must have runbooks.
At minimum:
API outage
Database outage
Redis outage
AI outage
Payment outage
Notification outage
Deployment failure
Security incident
Data integrity issue
17.17 Deployment Monitoring
Every deployment must be observable.
Monitor:
error rate
latency
traffic
CPU
memory
database behavior
queue behavior
Compare pre-deployment and post-deployment metrics.
17.18 Audit vs Operational Logs
These are different systems.
Operational logs
Used for:
debugging
performance
errors
system behavior
Audit logs
Used for:
who did what
security
administrative actions
compliance
Audit records must not be treated as ordinary debug logs.
17.19 Incident Evidence
During an incident collect:
deployment version
request IDs
timestamps
logs
metrics
traces
affected endpoints
affected users
database state
queue state
provider status
17.20 Postmortem
Every significant incident should document:
Summary
Impact
Timeline
Root Cause
Contributing Factors
Detection
Response
Resolution
What Went Well
What Went Wrong
Corrective Actions
Preventive Actions
No blame-oriented language.
18 — W8 Testing & Quality Engineering
18.1 Purpose
Testing is a mandatory engineering layer.
A feature is not complete because it works manually once.
A feature is complete when:
Requirements are implemented.
Automated tests exist.
Security rules are validated.
Error paths are covered.
Accessibility is verified where applicable.
Regression risk is controlled.
18.2 Testing Pyramid
             E2E
            /   \
       Integration
        /        \
   Unit Tests   Contract Tests
Most tests should remain fast unit tests.
18.3 Backend Unit Testing
Test:
Domain rules.
Services.
Validators.
Authorization.
Entitlement logic.
Subscription state transitions.
Recommendation rules.
Notification policies.
External providers should normally be mocked at unit-test level.
18.4 Backend Integration Testing
Integration tests should verify:
API
 ↓
Database
 ↓
Redis
 ↓
Queue
Examples:
User registration.
Workout completion.
Content publishing.
Subscription update.
Notification creation.
18.5 API Contract Testing
Every public API contract must have tests for:
Request schema.
Response schema.
Authentication.
Authorization.
Validation.
Error contract.
Breaking API changes must fail CI.
18.6 Database Testing
Test:
Constraints.
Unique indexes.
Foreign keys.
Migrations.
Transactions.
Rollbacks.
Data integrity.
Every migration must be tested from a clean database.
18.7 Flutter Unit Tests
Test:
Models.
Repositories.
State management.
Validators.
Formatters.
Localization.
Entitlement logic.
Workout calculations.
18.8 Flutter Widget Tests
Test important UI states:
Loading
Success
Empty
Error
Offline
Premium locked
Arabic
English
Dark mode
Light mode
18.9 Flutter Integration Tests
Critical flows:
Onboarding
Login
Workout start
Exercise logging
Workout completion
Nutrition interaction
AI Coach
Subscription purchase
Restore purchase
Notifications
Settings
Logout
18.10 Admin Testing
Test:
Login.
RBAC.
User search.
User suspension.
Content creation.
Content review.
Content publishing.
Content rollback.
Notification creation.
Subscription visibility.
Audit logs.
18.11 Security Testing
Automate:
Dependency vulnerability scanning.
Secret scanning.
Static analysis.
Authentication tests.
Authorization tests.
Input validation tests.
Rate-limit tests.
IDOR/BOLA tests.
Access-control regression tests.
18.12 AI Testing
AI requires specialized testing.
Test:
Prompt correctness
Schema compliance
Safety behavior
Unsupported questions
Language handling
Arabic/English
Context isolation
Hallucination resistance
Rate limits
Provider failure
AI output should be tested against deterministic evaluation criteria whenever possible.
18.13 AI Regression Tests
Maintain a fixed evaluation dataset:
Question
Expected category
Required behavior
Forbidden behavior
Expected safety level
Every major prompt/model change should run the evaluation suite.
18.14 Payment Testing
Use sandbox environments.
Test:
Successful purchase
Failed purchase
Cancelled purchase
Renewal
Expired subscription
Refund
Revocation
Restore
Duplicate webhook
Out-of-order webhook
Webhook replay
No production payment credentials may be used in automated tests.
18.15 Notification Testing
Test:
Permission denied.
Permission granted.
Token refresh.
Invalid token.
Duplicate event.
Retry.
Failure.
Scheduling.
Timezones.
Localization.
18.16 Performance Testing
Test:
API throughput.
Database performance.
Concurrent users.
Queue throughput.
AI latency.
Admin large datasets.
Load testing must run against isolated environments.
18.17 Accessibility Testing
Verify:
Screen reader semantics.
Focus order.
Keyboard navigation where applicable.
Minimum touch targets.
Contrast.
Text scaling.
RTL behavior.
18.18 Localization Testing
Both locales must be tested:
English
Arabic
Verify:
Translation completeness.
RTL layout.
Text overflow.
Numbers.
Dates.
Time.
Plurals.
18.19 Regression Testing
Every bug fixed should receive a regression test where practical.
Rule:
Bug fixed
   ↓
Root cause identified
   ↓
Regression test added
18.20 Quality Gates
A pull request must not merge if:
Tests fail
Lint fails
Type checks fail
Security scan fails
Build fails
Required coverage threshold fails
Secret scan detects credentials
Critical security findings must block release.
19 — W8 CI/CD, DevSecOps & Deployment
19.1 Purpose
W8 uses automated CI/CD to provide:
Repeatable builds.
Automated testing.
Security validation.
Artifact generation.
Environment promotion.
Deployment rollback.
No production deployment should depend on a developer manually copying files to a server.
19.2 Git Workflow
Recommended:
main
 │
 ├── feature/*
 ├── fix/*
 ├── chore/*
 └── security/*
Production releases must originate from controlled branches/tags.
19.3 Pull Request Requirements
Every PR should include:
Description.
Scope.
Tests.
Security considerations.
Database migration information if applicable.
API changes if applicable.
Screenshots for relevant UI changes.
19.4 CI Pipeline
Baseline:
Push / PR
   ↓
Checkout
   ↓
Dependency Install
   ↓
Format Check
   ↓
Lint
   ↓
Type Check
   ↓
Unit Tests
   ↓
Integration Tests
   ↓
Security Scan
   ↓
Build
   ↓
Artifact
19.5 Backend CI
Run:
Formatting
Linting
Type checking
Unit tests
Integration tests
Migration validation
Dependency scanning
Secret scanning
Container build
19.6 Flutter CI
Run:
Dart formatting
Analyzer
Unit tests
Widget tests
Integration tests where configured
Dependency scan
Build validation
19.7 Admin CI
Run:
Formatting
Lint
Type checking
Unit tests
Component tests
Build
Dependency scan
19.8 Infrastructure CI
Terraform/infrastructure changes must run:
Format
Validate
Lint
Security scan
Plan
Production apply requires controlled authorization.
19.9 DevSecOps Pipeline
Security is integrated into CI.
Code
 ↓
SAST
 ↓
Dependency Scan
 ↓
Secret Scan
 ↓
Container Scan
 ↓
IaC Scan
 ↓
Tests
 ↓
Build
Tools may include:
Semgrep
Trivy
Gitleaks
OWASP tooling
Terraform security scanners
Tool choice can evolve without changing the security requirements.
19.10 Secret Management
Secrets must never be committed to Git.
Forbidden:
.env
API keys
database passwords
JWT secrets
payment credentials
AI provider keys
cloud credentials
Use:
CI/CD secret stores.
Cloud secret managers.
Environment-specific secret management.
19.11 Environment Separation
At minimum:
Development
Staging
Production
Each environment must have isolated:
Database.
Redis.
Storage.
API credentials.
AI configuration.
Payment configuration where appropriate.
Production credentials must never be used in development.
19.12 Containerization
Backend services should use reproducible containers.
Requirements:
Minimal base images.
Non-root user.
Pinned dependencies where practical.
Multi-stage builds.
No secrets inside images.
Image vulnerability scanning.
19.13 Image Tagging
Avoid using only:
latest
Use immutable version identifiers.
Example:
w8-api:1.4.0
w8-api:git-abc123
19.14 Deployment Strategy
Recommended:
Development
    ↓
CI
    ↓
Staging
    ↓
Automated Validation
    ↓
Approval
    ↓
Production
19.15 Database Migrations
Migrations must:
Be version-controlled.
Be reviewed.
Be backward-compatible where required.
Be tested in CI.
Have rollback/recovery strategy where feasible.
Production migrations must not depend on manual SQL execution from a developer laptop.
19.16 Zero-Downtime Migration Principle
For breaking schema changes:
Expand
 ↓
Deploy compatible application
 ↓
Backfill
 ↓
Switch reads/writes
 ↓
Contract
Do not deploy application code that immediately requires a database column that does not yet exist.
19.17 Deployment Rollback
Every production deployment must have a rollback strategy.
Rollback may mean:
Previous application version
But database migrations require separate consideration.
Never blindly rollback application code if the new schema is incompatible.
19.18 Mobile Release
Mobile releases must use appropriate store distribution pipelines.
Stages:
Development Build
 ↓
Internal Testing
 ↓
Staging/Beta
 ↓
Release Candidate
 ↓
Production
Production builds must use production configuration without embedding development secrets.
19.19 Feature Flags
Feature flags should be used for risky features.
Example:
ai_coach_enabled
new_workout_engine
new_nutrition_builder
premium_v2
Flags must be evaluated server-side where security or entitlement is involved.
Feature flags must not replace authorization.
19.20 Deployment Verification
After deployment:
Health Check
 ↓
Smoke Tests
 ↓
Metrics Check
 ↓
Error Check
 ↓
Critical Flow Verification
Critical flows:
Login
API
Workout
Nutrition
AI
Notifications
Subscription
Admin
19.21 CI/CD Protection
Production deployment credentials must be:
Restricted.
Short-lived where possible.
Environment-scoped.
Audited.
Unavailable to arbitrary pull requests.
Fork-based PRs must never receive production secrets.
19.22 Supply Chain Security
Dependencies must be monitored.
Requirements:
Lock files.
Dependency scanning.
Renovation/update process.
Vulnerability alerts.
Approved package policy.
Remove unused dependencies.
19.23 Release Versioning
Use consistent semantic versioning where appropriate:
MAJOR.MINOR.PATCH
API versions must have an explicit compatibility strategy.
19.24 Deployment Audit
Record:
deployment_id
version
commit_sha
environment
actor
timestamp
result
rollback
20 — W8 Performance, Scalability & Cost
20.1 Purpose
W8 must be designed to remain responsive, reliable, and financially sustainable as usage grows.
Performance optimization must not compromise:
Security.
Data integrity.
Maintainability.
Correctness.
20.2 Performance Principles
Priorities:
Correctness
 ↓
Security
 ↓
Reliability
 ↓
Performance
 ↓
Cost optimization
Never optimize by bypassing security or domain validation.
20.3 Performance Targets
Initial targets should be defined as engineering goals rather than absolute guarantees.
Example:
API
p50 < 200 ms
p95 < 500 ms
p99 < 1 s
for normal lightweight endpoints under expected load.
Heavy operations may have different targets.
20.4 Mobile Performance
Target:
Fast startup.
Smooth scrolling.
No unnecessary rebuilds.
Efficient image loading.
Efficient caching.
Minimal network requests.
Proper pagination.
Avoid loading the entire exercise library into memory.
20.5 API Performance
Use:
Async I/O where appropriate.
Connection pooling.
Pagination.
Efficient serialization.
Database indexes.
Caching.
Background jobs.
20.6 Database Optimization
Indexes should exist for frequently queried fields.
Examples:
user_id
status
created_at
updated_at
slug
subscription_status
published_at
Indexes must be based on actual query patterns.
Do not create indexes blindly.
20.7 N+1 Prevention
Backend code must avoid N+1 query patterns.
Example:
Bad:
Get 100 workouts
 ↓
100 individual exercise queries
Preferred:
Get workouts
 ↓
Batch related data
20.8 Pagination
All potentially large collections must paginate.
Examples:
Exercises
Users
Notifications
Workout history
AI conversations
Audit logs
Transactions
20.9 Cursor Pagination
For high-volume datasets, cursor pagination should be preferred where appropriate.
Example:
GET /notifications?cursor=abc123&limit=50
20.10 Caching Strategy
Potential cache layers:
Flutter local cache
CDN
Redis
Backend application cache
Database
Cache only data where stale values are acceptable.
20.11 Cache Invalidation
Published content changes must invalidate relevant caches.
Example:
Exercise published
 ↓
Invalidate exercise cache
 ↓
Next request receives new version
Caching must never allow revoked/sensitive access to persist beyond security requirements.
20.12 Redis Usage
Redis may be used for:
Caching.
Rate limiting.
Session-related ephemeral state.
Job queues.
Distributed locks where justified.
Redis must not become the authoritative permanent data store for core business data.
20.13 Background Jobs
Long-running operations must be asynchronous.
Examples:
AI generation
Bulk content import
Notification campaigns
Analytics processing
Data export
Media processing
Large reports
20.14 Queue Architecture
API
 ↓
Queue
 ↓
Worker
 ↓
Job
 ↓
Result/Event
Jobs must support:
Retry.
Idempotency.
Timeout.
Dead-letter handling.
20.15 AI Performance
AI is potentially the most expensive external service.
Optimize using:
Model selection.
Context reduction.
Token limits.
Prompt compression.
Caching safe responses.
Retrieval filtering.
Request batching where appropriate.
Rate limiting.
Do not send the complete user history with every request.
20.16 AI Cost Controls
Track:
tokens/request
tokens/user/day
tokens/user/month
cost/request
cost/user
model usage
Define budget thresholds.
If a model becomes unavailable or too expensive, use an approved fallback strategy.
20.17 Image Optimization
Exercise and nutrition media should support:
Compression.
Responsive sizes.
WebP/AVIF where appropriate.
CDN delivery.
Lazy loading.
Thumbnail generation.
Do not ship original high-resolution images to every mobile device.
20.18 CDN
Static media should use a CDN.
Recommended assets:
Exercise images
Exercise videos
Food images
Recipe images
Article images
Static assets
Access-controlled/private assets require signed URLs or equivalent mechanisms.
20.19 Scalability Model
Initial architecture may use:
Load Balancer
      ↓
API Instances
      ↓
PostgreSQL
      +
Redis
      +
Workers
Horizontal scaling should be possible without application changes.
20.20 Stateless API
API instances should be stateless where possible.
Do not store critical session/application state only in local process memory.
This enables:
Instance 1
Instance 2
Instance 3
to serve the same user.
20.21 Horizontal Scaling
Scale independently:
API
Workers
AI workers
Notification workers
Admin
Do not scale the entire platform when only one workload requires scaling.
20.22 Database Scaling
Initial strategy:
Primary PostgreSQL
Future options:
Read replicas
Connection pooling
Partitioning
Analytics database
Caching
Do not introduce database sharding before actual evidence requires it.
20.23 Analytics Isolation
Heavy analytics queries must not degrade transactional operations.
Possible architecture:
Production DB
      ↓
Events / ETL
      ↓
Analytics Storage
20.24 Rate Limiting
Rate limits must exist for:
Authentication
Password reset
AI requests
Search
Admin APIs
Notification creation
Payment endpoints
Public APIs
Limits should be defined per:
IP
User
Endpoint
API key/service
depending on the endpoint.
20.25 Cost Monitoring
Track infrastructure cost by:
Compute
Database
Storage
CDN
AI
Notifications
Payment infrastructure
Monitoring
CI/CD
20.26 Cost Allocation
Where possible track cost by service:
API
AI
Database
Storage
Notifications
Admin
AI cost should additionally be tracked per:
user
model
feature
request type
20.27 Autoscaling
Autoscaling signals may include:
CPU
Memory
Request rate
Latency
Queue depth
Queue workers should scale based on backlog rather than CPU alone when appropriate.
20.28 Capacity Planning
Before major growth milestones estimate:
Daily Active Users
Requests/second
Database operations
Storage growth
AI requests/day
Notification volume
Example capacity model:
DAU
 ↓
Sessions/user
 ↓
Requests/session
 ↓
Requests/day
 ↓
Peak requests/second
Capacity planning must use observed production metrics once available.
20.29 Performance Budgets
Define budgets for:
Mobile
Startup time
Frame rendering
Memory
APK/IPA size
Network payload
Backend
Latency
CPU
Memory
Database queries
Payload size
AI
Latency
Tokens
Cost/request
Budgets should be monitored continuously.
20.30 Performance Regression Prevention
Performance-sensitive code must include benchmarks or measurements when appropriate.
Examples:
Large exercise list rendering.
Workout plan loading.
Nutrition plan calculation.
Admin user search.
AI context generation.
Database queries.
20.31 Scalability Rules
The implementation must avoid:
1. Global mutable application state.
2. Local filesystem dependency for critical persistent data.
3. In-memory-only sessions.
4. Unbounded database queries.
5. Synchronous long-running requests.
6. AI calls inside database transactions.
7. Notification sending inside user-facing transactions.
8. Large synchronous content imports.
9. Full-table scans on frequently used endpoints.
10. Loading entire datasets into mobile memory.
Cross-Section Integration: Payments → Observability → Testing → CI/CD → Performance
These sections must operate as one engineering lifecycle:
                 ┌──────────────────────┐
                 │       Developer      │
                 └──────────┬───────────┘
                            │
                            ▼
                    Git Pull Request
                            │
                            ▼
                 ┌──────────────────────┐
                 │       CI Pipeline    │
                 │                      │
                 │ Lint                 │
                 │ Tests                │
                 │ Security             │
                 │ Build                │
                 │ Dependency Scan      │
                 └──────────┬───────────┘
                            │
                            ▼
                         Staging
                            │
                            ▼
                  Automated Validation
                            │
                            ▼
                        Production
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
     Observability      Payments         Application
     Logs/Metrics       Monitoring        Traffic
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ▼
                     Incident Detection
                            │
                            ▼
                       Runbook/Response
                            │
                            ▼
                       Postmortem
                            │
                            ▼
                      Engineering Fix
                            │
                            ▼
                         CI Again
W8 Non-Negotiable Engineering Invariants — Sections 16–20
The implementation AI MUST NOT violate the following:
Premium access must never depend solely on Flutter state.
Payment transactions must be verified server-side.
Payment webhooks must be authenticated and idempotent.
Subscription state and entitlement state must remain separate concepts.
Payment credentials must never be stored by W8 unless explicitly required by a compliant payment architecture.
Every critical payment transition must be auditable.
Operational logs and audit logs must remain separate concerns.
Secrets must never appear in logs.
Every production service must expose health/readiness information.
Critical services must have actionable monitoring and alerts.
Critical user flows must have automated tests.
Security testing must run in CI.
Dependency and secret scanning must run automatically.
Production deployments must be reproducible.
Production secrets must never be available to arbitrary PRs.
Database migrations must be version-controlled and tested.
Every production deployment must have a rollback/recovery strategy.
No deployment should rely on manual server file copying.
Long-running work must use background jobs.
APIs must remain horizontally scalable.
Large datasets must use pagination.
AI requests must be rate-limited and cost-controlled.
AI must not receive unrestricted database access.
Analytics must not degrade transactional workloads.
Caching must never bypass authorization.
Performance optimization must never bypass security or business rules.
Every important performance assumption must eventually be validated using real metrics.
Cost must be observable per major infrastructure/service category.
External providers must be accessed through abstraction layers.
A feature is not production-ready until it passes its functional, security, reliability, observability, and performance requirements.
Section 16–20 Completion Criteria
These sections are considered implemented only when W8 has:
✓ Server-side subscription verification
✓ Entitlement engine
✓ Idempotent payment processing
✓ Secure webhook processing
✓ Subscription restoration
✓ Structured logging
✓ Request correlation
✓ Metrics
✓ Health checks
✓ Error monitoring
✓ Incident runbooks
✓ Automated backend tests
✓ Automated Flutter tests
✓ API contract tests
✓ Security tests
✓ AI evaluation tests
✓ Payment sandbox tests
✓ CI pipeline
✓ SAST/dependency/secret scanning
✓ Staging environment
✓ Production deployment pipeline
✓ Rollback strategy
✓ Database migration strategy
✓ Rate limiting
✓ Caching strategy
✓ Background job architecture
✓ Performance budgets
✓ AI cost controls
✓ Infrastructure cost monitoring
✓ Capacity planning
End of Sections 16–20.
