تمام. دول آخر 4 Sections، وهنخليهم Implementation-grade لأنهم أهم جزء بالنسبة لطريقة استخدام الـAI في بناء W8. خصوصا إنك هتعتمد على AI في تنفيذ المشروع، فلازم الوثيقة تمنع الـAI من إنه "يخترع" architecture أو يعمل shortcuts.
النص التالي جاهز يتحط مباشرة بعد Section 25 داخل:
docs/W8_MASTER_TECHNICAL_SPECIFICATION.md
26 — W8 Environment & Infrastructure Specification
26.1 Purpose
This section defines the environments, infrastructure boundaries, configuration management, deployment topology, networking, secrets, external services, and operational infrastructure required to run W8 safely.
The infrastructure must support:
Local development
Automated testing
CI/CD
Staging
Production
Disaster recovery
Observability
Secure secret management
Horizontal scaling
Controlled deployments
Infrastructure must be reproducible.
No production infrastructure should depend on undocumented manual configuration.
26.2 Environment Model
W8 MUST use separate environments.
local
  ↓
CI
  ↓
staging
  ↓
production
Recommended environment separation:
Environment
Purpose
Local
Developer/AI development
CI
Automated tests/builds
Staging
Production-like validation
Production
Real users
26.3 Local Development
The entire backend development environment should be reproducible using Docker Compose.
Recommended local services:
PostgreSQL
Redis
API
Worker
Scheduler
Admin
Optional local object storage
Optional observability stack
Example:
docker compose up
should be sufficient to start the core development environment.
The AI must not require developers to manually install multiple infrastructure dependencies unless explicitly documented.
26.4 Recommended Repository Infrastructure
infrastructure/
├── docker/
├── terraform/
│   ├── modules/
│   ├── environments/
│   │   ├── staging/
│   │   └── production/
│   └── variables/
├── kubernetes/
│   ├── base/
│   └── overlays/
├── monitoring/
└── scripts/
The exact deployment technology may evolve, but infrastructure configuration must remain version-controlled.
26.5 Containerization
All backend runtime services should be containerized.
Expected services:
w8-api
w8-worker
w8-scheduler
w8-admin
Containers must:
run as non-root where possible
use minimal base images
pin important dependencies
expose only required ports
avoid embedding secrets
include health checks
produce structured logs
support graceful shutdown
26.6 Backend Runtime
The backend stack defined by the architecture should remain consistent.
Recommended:
Python
FastAPI
PostgreSQL
SQLAlchemy
Alembic
Redis
Celery or approved async worker architecture
The implementation must not introduce another backend framework simply because an AI coding tool prefers it.
26.7 Database
Production database:
PostgreSQL
Requirements:
automated backups
point-in-time recovery where supported
encryption at rest
TLS connections
connection pooling
monitoring
migration management
restricted network access
The API must never expose PostgreSQL directly to the public Internet.
26.8 Redis
Redis may be used for:
caching
rate limiting
distributed locks
background-job coordination
temporary state
Redis must not become the authoritative source of permanent business data unless explicitly specified.
26.9 Object Storage
Object storage should be used for large/non-relational assets.
Examples:
Exercise images
Exercise videos
User-uploaded assets
Content media
Generated exports
Reports
The database should store metadata and object references rather than large binary payloads.
26.10 CDN
Public/static content should be delivered through a CDN where appropriate.
Examples:
Exercise images
Public content
Static assets
Application assets
Private user assets must use controlled access, such as signed URLs.
26.11 Network Architecture
Production should follow a layered architecture.
Internet
   ↓
CDN / WAF / Load Balancer
   ↓
API / Web services
   ↓
Private services
   ├── PostgreSQL
   ├── Redis
   ├── Workers
   └── Internal services
Databases and internal services must not be publicly reachable.
26.12 TLS
All production network traffic containing sensitive data must use TLS.
Required:
HTTPS
TLS database connections where applicable
Secure external API connections
HTTP should redirect to HTTPS where applicable.
26.13 Secrets Management
Secrets include:
Database credentials
JWT signing keys
OAuth secrets
Payment provider secrets
AI provider keys
Push notification credentials
Storage credentials
Admin secrets
Secrets must never be committed to Git.
Never:
.env.production
API keys
private keys
database passwords
inside the repository.
26.14 Environment Variables
Configuration must be environment-specific.
Example:
APP_ENV
DATABASE_URL
REDIS_URL
JWT_SECRET
AI_PROVIDER_KEY
PAYMENT_PROVIDER_KEY
STORAGE_BUCKET
LOG_LEVEL
Environment variables must be validated at application startup.
Missing required configuration should fail fast.
26.15 Configuration Rules
Configuration must be separated into:
Application configuration
Infrastructure configuration
Secrets
Feature flags
Never use secrets as feature flags.
Never use feature flags to bypass authorization.
26.16 Health Checks
Every production service must expose health information.
Minimum:
/health
Recommended separation:
/liveness
/readiness
Liveness
Checks whether the process is alive.
Readiness
Checks whether the service is capable of receiving traffic.
26.17 Graceful Shutdown
Services must handle shutdown signals correctly.
The API must:
stop accepting new work
finish safe in-flight requests
close database connections
close Redis connections
stop workers safely
26.18 Resource Limits
Production workloads must define:
CPU requests
CPU limits
Memory requests
Memory limits
Where the deployment platform supports them.
This prevents a single service from consuming unlimited resources.
26.19 Autoscaling
Services that support horizontal scaling should be stateless.
Scaling signals may include:
CPU
Memory
Request rate
Latency
Queue depth
Workers should primarily scale according to workload/queue pressure.
26.20 Infrastructure as Code
Production infrastructure must be reproducible through Infrastructure as Code.
Preferred:
Terraform
Infrastructure changes must go through:
Git
 ↓
Review
 ↓
CI validation
 ↓
Plan
 ↓
Approval
 ↓
Apply
No undocumented manual production modifications.
26.21 Infrastructure Security
Infrastructure must enforce:
least privilege
private database networking
restricted administrative access
firewall rules
WAF/rate limiting where appropriate
encrypted storage
encrypted traffic
secret isolation
auditability
26.22 Infrastructure Backups
Infrastructure configuration is stored in Git.
State files for infrastructure tools must be:
remotely stored
encrypted
access controlled
backed up
Secrets must not be exposed through infrastructure state.
26.23 External Services
Every external service must have:
Provider
Purpose
API
Credentials
Environment configuration
Rate limits
Failure behavior
Fallback
Monitoring
Cost
Examples:
AI Provider
Payment Provider
Push Notification Provider
Health Integration
Object Storage
Email Provider
Analytics
The application must not tightly couple core business logic to a single external provider where avoidable.
26.24 External Service Failure
External services must fail gracefully.
Example:
AI unavailable
    ↓
AI feature temporarily unavailable
    ↓
Core application continues
Similarly:
Payment provider unavailable
    ↓
Do not grant entitlement
    ↓
Record failure
    ↓
Retry/webhook reconciliation
26.25 Environment Security Rules
Local
Test credentials and development data only.
CI
Ephemeral credentials where possible.
Staging
Separate credentials and infrastructure.
Production
Production credentials only.
No production credentials may be reused in local development.
27 — W8 AI Development Rules
27.1 Purpose
W8 will be developed with substantial assistance from AI coding agents.
Therefore, AI must operate under strict engineering constraints.
The AI is an implementation agent, not the product architect.
The AI must follow:
W8_MASTER_TECHNICAL_SPECIFICATION.md
        ↓
AGENTS.md
        ↓
Implementation Milestone
        ↓
Task specification
        ↓
Existing repository architecture
27.2 Source-of-Truth Hierarchy
When instructions conflict:
1. Security requirements
2. W8 Master Technical Specification
3. AGENTS.md
4. Current milestone specification
5. Existing approved architecture
6. Approved prototype
7. AI implementation preference
AI preference is always the lowest priority.
27.3 AI Must Not Invent Requirements
The AI must not invent:
business rules
pricing
permissions
medical claims
nutrition claims
exercise safety claims
API contracts
database relationships
security exceptions
product behavior
If a requirement is genuinely missing:
STOP
↓
Identify ambiguity
↓
Document it
↓
Ask for decision
The AI must not silently guess.
27.4 AI Must Not Rewrite Architecture
The AI must not replace:
FastAPI
PostgreSQL
Flutter
Admin architecture
Redis
with alternatives simply because another technology is easier.
Architecture changes require explicit approval.
27.5 Small Incremental Changes
The AI should implement one task at a time.
Preferred:
Task
 ↓
Implementation
 ↓
Tests
 ↓
Lint
 ↓
Type/static checks
 ↓
Review
 ↓
Commit
Avoid giant uncontrolled changes.
27.6 Read Before Editing
Before modifying code, AI must inspect:
AGENTS.md
Master Specification
Relevant milestone
Relevant source files
Existing tests
Existing interfaces
The AI must understand the existing implementation before creating new abstractions.
27.7 Reuse Before Creating
Before creating a new:
service
component
repository
utility
validator
API client
database helper
AI must search the repository for an existing implementation.
Duplicate functionality is prohibited unless there is a documented reason.
27.8 Security-First Implementation
Every feature must consider:
Authentication
Authorization
Input validation
Output validation
Rate limiting
Secrets
Logging
PII exposure
Injection
IDOR
CSRF where applicable
CORS
File upload security
Abuse prevention
Security must not be postponed until M14 if the feature is already being implemented earlier.
27.9 No Hardcoded Secrets
AI must never generate:
API_KEY="..."
PASSWORD="..."
JWT_SECRET="..."
with realistic-looking production credentials.
Use:
environment variables
secret manager
example configuration
27.10 No Fake Implementations
AI must not leave fake production behavior such as:
TODO
FIXME
pass
mock response
hardcoded success
fake database
fake payment confirmation
fake authentication
unless the milestone explicitly defines the item as a temporary mock.
Temporary mocks must be clearly marked and tracked.
27.11 No Silent Fallbacks
The AI must not silently hide failures.
Bad:
API fails → return empty list
when the user needs to know the operation failed.
Correct:
failure
 ↓
structured error
 ↓
logging
 ↓
UI error state
27.12 Database Rules
AI must:
use migrations
never manually mutate production schema
use constraints
use indexes intentionally
use transactions where required
avoid N+1 queries
avoid destructive migrations without safeguards
27.13 API Rules
Every API endpoint must define:
Request
Authentication
Authorization
Validation
Response
Errors
Status codes
Rate limits where relevant
Observability
API behavior must conform to Section 06.
27.14 Flutter Rules
Flutter code must:
follow Section 10
follow Section 25
use localization
use semantic widgets
avoid business logic inside widgets
avoid direct database access
avoid direct secret access
handle loading/error/empty states
support Arabic RTL
support dark/light themes
27.15 AI Coach Rules
The AI must not present itself as a medical professional.
AI responses must follow the safety rules defined in Section 13.
AI must not:
diagnose disease
prescribe medication
invent medical facts
fabricate exercise science
fabricate nutritional data
claim certainty where uncertainty exists
Safety-critical recommendations must have controlled behavior.
27.16 Content Rules
AI must not automatically publish:
exercise programs
nutrition plans
health claims
educational content
Content must go through the W8 CMS workflow.
Draft
 ↓
Review
 ↓
Approve
 ↓
Publish
27.17 Testing Requirement
AI-generated code is not considered complete until tests are created or updated.
At minimum:
Unit tests
Integration tests
API tests
Widget tests where applicable
Security tests where applicable
Critical flows require end-to-end coverage.
27.18 Verification Loop
After implementation:
Run formatter
 ↓
Static analysis
 ↓
Unit tests
 ↓
Integration tests
 ↓
Build
 ↓
Security checks
 ↓
Review diff
The AI must report actual results.
It must never say:
"Tests passed"
unless tests were actually executed successfully.
27.19 No False Claims
AI must distinguish:
Implemented
Tested
Partially implemented
Not implemented
Blocked
Needs decision
Never report planned functionality as completed.
27.20 Change Documentation
Significant architecture decisions must be documented.
Examples:
Why Riverpod was selected
Why a payment provider was selected
Why a database index exists
Why an AI provider is used
Why data retention has a certain policy
27.21 Git Discipline
AI should make small logical commits.
Example:
feat(auth): add refresh token rotation
test(auth): add refresh token security tests
fix(workout): prevent duplicate session creation
Avoid:
update stuff
changes
final
AI changes
27.22 AI Stop Conditions
AI must stop and request clarification when:
specification conflicts exist
security behavior is ambiguous
destructive migration is required
payment behavior is unclear
user-data handling is unclear
medical/nutrition safety behavior is unclear
authentication behavior is unclear
architectural change is required
28 — W8 Definition of Done
28.1 Purpose
A task is Done only when implementation, testing, security, documentation, and verification requirements have been satisfied.
Code existing in the repository does not automatically mean the task is complete.
28.2 Task-Level Definition of Done
A task is Done when:
Functional
requirements implemented
expected behavior verified
edge cases handled
error states handled
Code Quality
clean code
correct architecture
no unnecessary duplication
no dead code
no unexplained hacks
Security
authentication considered
authorization implemented
input validated
sensitive data protected
secrets protected
Testing
relevant tests written
tests pass
regression tests added where necessary
UI
For frontend tasks:
loading state
empty state
error state
disabled state where applicable
accessibility
RTL
localization
dark/light theme
responsive behavior
Observability
Relevant operations must produce appropriate:
logs
metrics
traces
audit events
28.3 Feature-Level Definition of Done
A feature is Done when:
Specification
     ↓
Backend
     ↓
Database
     ↓
API
     ↓
Frontend
     ↓
Validation
     ↓
Authorization
     ↓
Tests
     ↓
Observability
     ↓
Documentation
are complete.
28.4 Backend DoD
Backend feature must include:
API implementation
validation
authorization
database changes
migration
error handling
logging
tests
OpenAPI/API documentation
performance consideration
28.5 Flutter DoD
Flutter feature must include:
UI
state management
API integration
loading
empty
error
offline behavior where applicable
localization
RTL
accessibility
theme support
responsive layout
tests
28.6 Admin DoD
Admin feature must include:
permission enforcement
search
filtering where required
pagination where required
loading
empty
error
success
confirmation for destructive actions
audit logging
responsive behavior
28.7 CMS DoD
Content feature must include:
Create
Read
Update
Delete/Archive
Draft
Review
Publish
Unpublish
Version
Audit
Where applicable.
28.8 AI Feature DoD
AI feature must include:
prompt/version management
input validation
context restrictions
output validation
safety handling
timeout
retry policy
provider failure handling
usage monitoring
cost monitoring
logging without sensitive-data leakage
evaluation tests
28.9 Payment DoD
Payment functionality must include:
provider integration
secure webhook verification
idempotency
transaction state machine
entitlement reconciliation
failure handling
duplicate-event protection
auditability
testing with provider sandbox
28.10 Production DoD
Before production:
All critical tests pass
Security checks pass
Database migrations tested
Backups verified
Restore tested
Monitoring active
Alerts configured
Secrets configured
Rate limits configured
Rollback tested
Staging validated
Release approved
28.11 Forbidden Definition of Done
The following are NOT sufficient:
"It works on my machine."
"The UI looks correct."
"The API returns 200."
"The AI generated the code."
"No errors appeared during development."
29 — W8 Implementation Milestones
29.1 Purpose
W8 implementation must proceed through controlled milestones.
Each milestone contains:
Objective
Scope
Dependencies
Tasks
Acceptance criteria
Tests
Security requirements
Deliverables
Exit criteria
The implementation must follow milestone order unless an explicit dependency allows parallel work.
29.2 Milestone Overview
The official implementation sequence is:
M0  Engineering Foundation
M1  Backend Foundation
M2  Identity
M3  Core User Domain
M4  Content Management
M5  Workout Engine
M6  Nutrition Engine
M7  Mobile Core UI
M8  AI Coach
M9  Personalization
M10 Notifications
M11 Payments
M12 Admin Dashboard
M13 Observability
M14 Security Hardening
M15 Testing & QA
M16 Staging
M17 Production Readiness
M18 Production Release
M19 Post-Launch
These milestones correspond to:
docs/implementation/
29.3 M0 — Engineering Foundation
Objective
Create a clean, reproducible, secure development foundation.
Includes
Repository
Monorepo structure
Documentation
AGENTS.md
Docker
Development environment
Linting
Formatting
Git hooks where appropriate
CI foundation
Environment configuration
Base testing infrastructure
Exit Criteria
Repository builds
Local environment starts
CI runs
Lint passes
Tests execute
No secrets committed
M1 — Backend Foundation
Objective
Create the production-grade backend foundation.
Includes
FastAPI
Configuration
Database
SQLAlchemy
Alembic
Redis
Error handling
API versioning
Health endpoints
Dependency injection
Base repository/service patterns
Exit Criteria
Backend starts cleanly and database migrations execute successfully.
M2 — Identity
Objective
Implement authentication and account lifecycle.
Includes
Registration
Login
Logout
Password management
Token lifecycle
Email verification
Session management
Account security
RBAC foundation
Exit Criteria
Authentication and authorization security tests pass.
M3 — Core User Domain
Objective
Implement user-owned domain data.
Includes
Profile
Goals
Preferences
Activity level
Training preferences
Progress
User settings
Exit Criteria
User data is correctly isolated by ownership.
M4 — Content Management
Objective
Build the CMS before importing the large exercise/workout/nutrition datasets.
This milestone is especially important for W8.
Includes
Exercise management
Workout plan management
Nutrition content
Categories
Tags
Media
Drafts
Review
Publishing
Versioning
Audit
Important Rule
Do not manually insert the final exercise/workout/nutrition datasets into production tables before the CMS is operational.
The intended workflow is:
Your Data
   ↓
CMS Import / Admin
   ↓
Validation
   ↓
Review
   ↓
Publish
   ↓
Production Database
M5 — Workout Engine
Objective
Implement the actual workout domain.
Includes
Workout plans
Sessions
Exercises
Sets
Repetitions
Weight
Duration
Rest
Logging
Completion
Progress
Workout history
Exit Criteria
Complete workout lifecycle works end-to-end.
M6 — Nutrition Engine
Objective
Implement nutrition functionality.
Includes
Meals
Foods
Macros
Calories
Plans
Daily tracking
Meal replacement
Nutrition history
Content must come from CMS-managed data.
M7 — Mobile Core UI
Objective
Build the Flutter application using the approved prototype and design system.
Includes
Theme
Localization
Navigation
Onboarding
Dashboard
Workout UI
Nutrition UI
History
Settings
Reusable components
Exit Criteria
Core mobile flows operate against real APIs rather than static prototype data.
M8 — AI Coach
Objective
Implement the W8 AI Coach safely.
Includes
Conversation
Messages
Streaming
Context
Prompt versioning
Safety
Provider integration
Error handling
Usage tracking
M9 — Personalization
Objective
Create the personalization/recommendation layer.
Includes
User goals
Training history
Nutrition behavior
Progress
Preferences
Recommendation engine
Adaptive plans
AI-assisted personalization
Recommendations must respect the business and safety rules.
M10 — Notifications
Objective
Implement communication infrastructure.
Includes
Push notifications
Preferences
Notification history
Templates
Events
Scheduling
Permission states
Retry handling
Deep links
M11 — Payments
Objective
Implement monetization and entitlement enforcement.
Includes
Products
Subscriptions
Transactions
Entitlements
Webhooks
Restore
Cancellation
Upgrade
Downgrade
Reconciliation
Backend entitlement state is authoritative.
M12 — Admin Dashboard
Objective
Implement operational administration.
Includes
Dashboard
Users
Exercises
Workout Plans
Nutrition
AI
Notifications
Subscriptions
Analytics
Settings
Admin actions must respect RBAC and generate audit events.
M13 — Observability
Objective
Make W8 observable in production.
Includes
Structured logging
Metrics
Tracing
Dashboards
Alerts
Audit logs
Error tracking
Performance monitoring
M14 — Security Hardening
Objective
Perform dedicated security hardening after the primary functionality exists.
This does NOT mean security was ignored earlier.
Includes
Dependency scanning
SAST
DAST where applicable
Container scanning
Secret scanning
Authorization testing
Rate-limit testing
IDOR testing
Injection testing
Session security
Infrastructure security
M15 — Testing & QA
Objective
Validate the complete W8 system.
Testing layers
Unit
Integration
API
Widget
E2E
Security
Performance
Accessibility
Localization
Regression
Critical user journeys must be tested end-to-end.
M16 — Staging
Objective
Create a production-like staging environment.
Includes
Production-like infrastructure
Real migrations
Seeded safe content
External sandbox integrations
Monitoring
CI/CD
Smoke tests
E2E tests
No real production secrets or unnecessary real user data.
M17 — Production Readiness
Objective
Prove that W8 is ready for real users.
Checklist
Security
Performance
Backups
Restore
Monitoring
Alerts
Rollback
Database
Infrastructure
Payments
Notifications
AI
Privacy
Legal/compliance requirements
App-store requirements
M18 — Production Release
Objective
Safely release W8.
Process
Release candidate
 ↓
Final staging verification
 ↓
Backup
 ↓
Migration
 ↓
Deploy
 ↓
Smoke tests
 ↓
Limited rollout
 ↓
Monitoring
 ↓
Full rollout
M19 — Post-Launch
Objective
Operate and improve W8 after launch.
Includes
Incident management
Performance optimization
Cost optimization
User feedback
Product analytics
Security monitoring
Content updates
AI evaluation
Dependency updates
Technical debt
Feature improvements
Post-launch changes must continue to follow the Master Specification and AGENTS.md.
29.4 Milestone Task Structure
Every milestone file must follow the same structure.
Example:
M4_CONTENT_MANAGEMENT.md
must contain:
# M4 — Content Management

## 1. Objective

## 2. Scope

## 3. Dependencies

## 4. Architecture Context

## 5. Tasks

### M4-T01
### M4-T02
### M4-T03
...

## 6. Database Changes

## 7. API Changes

## 8. Admin Changes

## 9. Mobile Changes

## 10. Security Requirements

## 11. Tests

## 12. Acceptance Criteria

## 13. Definition of Done

## 14. Deliverables

## 15. AI Execution Instructions
29.5 Task Format
Every task must be atomic enough for an AI coding agent to execute safely.
Example:
M4-T03 — Exercise Entity & Migration
Objective
Create the exercise domain model and database migration.
Inputs
Section 03
Section 04
Section 08
Section 12
Requirements
define SQLAlchemy model
define constraints
define indexes
create Alembic migration
create repository
create tests
Security
no unauthorized access
admin permissions required for modification
Acceptance Criteria
Exercise can be created
Exercise can be retrieved
Invalid data is rejected
Duplicate identifiers are prevented
Unauthorized modification is rejected
Tests pass
Migration succeeds
29.6 AI Prompt Structure
Every implementation task should have an explicit AI execution instruction.
Recommended format:
You are implementing W8 task M4-T03.

Read:
1. AGENTS.md
2. W8_MASTER_TECHNICAL_SPECIFICATION.md
3. M4_CONTENT_MANAGEMENT.md

Before editing:
- inspect the existing repository
- identify reusable abstractions
- verify existing architecture

Implement only the requested task.

Do not:
- change architecture
- modify unrelated files
- invent requirements
- bypass security
- introduce unnecessary dependencies

After implementation:
1. format
2. lint
3. run relevant tests
4. inspect git diff
5. report files changed
6. report tests executed
7. report any remaining issues
29.7 Milestone Dependencies
The dependency graph is:
M0
 ↓
M1
 ↓
M2
 ↓
M3
 ↓
M4
 ├────────→ M5
 │            ↓
 │           M6
 │
 └────────→ M7
              ↓
             M8
              ↓
             M9

M2 ─────────→ M10
M2 ─────────→ M11
M4 ─────────→ M12
M1 ─────────→ M13
M0 ─────────→ M14
M5/M6/M7/M8/M9/M10/M11/M12
                ↓
               M15
                ↓
               M16
                ↓
               M17
                ↓
               M18
                ↓
               M19
Some milestones may be developed in parallel once their dependencies are satisfied.
29.8 Implementation Order Rule
The AI must not start with:
"Build the whole app."
Instead:
Milestone
   ↓
Task
   ↓
Implementation
   ↓
Verification
   ↓
Commit
   ↓
Next Task
This is critical for maintaining control over a large AI-generated codebase.
29.9 Milestone Completion Gate
A milestone cannot be marked complete merely because its code exists.
It must satisfy:
All tasks complete
        +
All tests pass
        +
Security requirements satisfied
        +
Documentation updated
        +
No critical TODOs
        +
No unresolved blockers
        +
Acceptance criteria verified
        =
Milestone Complete
29.10 Master Specification Completion
When Sections 01–29 are combined, the resulting document:
docs/W8_MASTER_TECHNICAL_SPECIFICATION.md
becomes the primary technical and product specification for W8.
It defines:
WHAT W8 is
WHAT W8 does
HOW W8 behaves
WHAT data W8 owns
WHAT rules W8 follows
WHAT security W8 requires
WHAT infrastructure W8 needs
WHAT quality means
WHEN implementation is considered complete
HOW implementation is phased
The repository then becomes:
w8/
│
├── AGENTS.md
│
├── docs/
│   ├── W8_MASTER_TECHNICAL_SPECIFICATION.md
│   │
│   ├── design/
│   │   ├── index.html
│   │   └── PROTOTYPE_AUDIT_REPORT.md
│   │
│   └── implementation/
│       ├── M0_ENGINEERING_FOUNDATION.md
│       ├── M1_BACKEND_FOUNDATION.md
│       ├── M2_IDENTITY.md
│       ├── M3_CORE_USER_DOMAIN.md
│       ├── M4_CONTENT_MANAGEMENT.md
│       ├── M5_WORKOUT_ENGINE.md
│       ├── M6_NUTRITION_ENGINE.md
│       ├── M7_MOBILE_CORE_UI.md
│       ├── M8_AI_COACH.md
│       ├── M9_PERSONALIZATION.md
│       ├── M10_NOTIFICATIONS.md
│       ├── M11_PAYMENTS.md
│       ├── M12_ADMIN_DASHBOARD.md
│       ├── M13_OBSERVABILITY.md
│       ├── M14_SECURITY_HARDENING.md
│       ├── M15_TESTING_QA.md
│       ├── M16_STAGING.md
│       ├── M17_PRODUCTION_READINESS.md
│       ├── M18_PRODUCTION_RELEASE.md
│       └── M19_POST_LAUNCH.md
│
├── apps/
│   ├── mobile/
│   ├── api/
│   └── admin/
│
├── packages/
├── infrastructure/
├── scripts/
├── tests/
│
├── .github/
│   └── workflows/
│
├── .gitignore
├── README.md
└── LICENSE
أهم نقطة قبل ما نبدأ التنفيذ
دلوقتي ما تبدأش تقول للـAI: "ابني W8".
إحنا وصلنا لمرحلة مختلفة تماما:
Master Specification = WHAT
AGENTS.md = HOW
M0–M19 = WHEN / IN WHAT ORDER
Tasks = EXACTLY WHAT THE AI DOES NOW
Prototype + Audit = DESIGN REFERENCE
وبما إنك عامل بالفعل ملفات الـ20 Milestones، فالخطوة العملية التالية ليست كتابة Sections جديدة. الخطوة التالية هي تجهيز M0 فعليا Task-by-Task.
يعني هنحول:
M0_ENGINEERING_FOUNDATION.md
من مجرد Milestone عام إلى شيء مثل:
M0-T01 — Repository Verification
M0-T02 — Monorepo Structure Verification
M0-T03 — Docker Development Environment
M0-T04 — Environment Configuration
M0-T05 — FastAPI Bootstrap
M0-T06 — Flutter Bootstrap
M0-T07 — Admin Bootstrap
M0-T08 — PostgreSQL Setup
M0-T09 — Redis Setup
M0-T10 — Alembic Setup
M0-T11 — Code Quality Tooling
M0-T12 — Pre-commit / Git Hooks
M0-T13 — CI Pipeline
M0-T14 — Test Infrastructure
M0-T15 — Security Baseline
M0-T16 — Documentation Verification
M0-T17 — Local Full-Stack Smoke Test
M0-T18 — M0 Final Verification
ولكل Task نكتب الـPrompt الذي ستعطيه للـAI، والـfiles المتوقع تعديلها، والـacceptance criteria، والـtests المطلوبة، والـsecurity checks.
ده في رأيي أهم شيء نعمله قبل أول سطر production code، لأنك لو عملت الـMaster ممتاز لكن سبت الـAI ينفذ Milestone كامل مرة واحدة، ممكن يرجع لنا بنفس مشكلة الـprototype: كود كتير، لكن gaps مخفية. أما الـTask-by-Task execution فبيخلي المشروع قابل للسيطرة والمراجعة حتى لو الـAI هو اللي بيكتب 90% من الكود.
