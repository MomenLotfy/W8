تمام. هنكمل بنفس فلسفة الـ W8 Master Technical Specification: مش مجرد شرح، لكن مواصفات قابلة للتنفيذ مباشرة بواسطة AI، وكل قرار فيها يكون واضح ومش قابل للتفسير بأكثر من طريقة.
مهم: الأقسام التالية مكتوبة بحيث تضعها مباشرة بعد Section 05 داخل:
docs/W8_MASTER_TECHNICAL_SPECIFICATION.md
والـ AGENTS.md هو اللي يحدد إزاي الـAI ينفذ، بينما الأقسام دي تحدد إيه اللي لازم يتنفذ.
06 — W8 API Contract & Backend Specification
6.1 Purpose
The W8 backend is the authoritative application server responsible for:
Authentication and identity.
User profiles.
User onboarding.
Workout plans and execution.
Exercise library.
Nutrition plans and meals.
AI Coach orchestration.
Personalization.
Notifications.
Subscriptions and entitlements.
Admin operations.
Audit logging.
Analytics and observability.
Security enforcement.
Data validation and business-rule enforcement.
The mobile application and Admin Dashboard MUST NOT directly access the primary database.
All application data access MUST go through authenticated backend APIs or explicitly approved backend-controlled mechanisms.
6.2 API Principles
The API MUST follow these principles:
RESTful HTTP APIs MUST be used for normal CRUD and domain operations.
APIs MUST be versioned.
Current API version MUST be:
/api/v1
The API MUST be stateless at the HTTP layer.
Authentication MUST be handled through secure tokens.
Authorization MUST be enforced server-side.
Client-side authorization checks are UX only and MUST NOT be trusted.
Request validation MUST happen before business logic.
Business rules MUST be enforced server-side.
Database models MUST NOT be exposed directly as API responses.
API responses MUST use explicit schemas.
Sensitive fields MUST NEVER be returned unnecessarily.
All write operations MUST be auditable where required.
API errors MUST use a consistent structure.
Pagination MUST be used for potentially large collections.
Filtering and sorting MUST be explicitly whitelisted.
Arbitrary database queries MUST NEVER be accepted from clients.
IDs exposed externally SHOULD use UUIDs.
API documentation MUST be generated from the FastAPI schemas.
Breaking API changes MUST require a new API version or explicit migration strategy.
6.3 Base API Structure
All APIs MUST follow:
/api/v1/<resource>
Examples:
/api/v1/auth
/api/v1/users
/api/v1/profile
/api/v1/onboarding
/api/v1/exercises
/api/v1/workouts
/api/v1/nutrition
/api/v1/coach
/api/v1/notifications
/api/v1/subscriptions
/api/v1/admin
6.4 Authentication Endpoints
Register
POST /api/v1/auth/register
Purpose:
Create a new user account.
Request:
{
  "email": "user@example.com",
  "password": "********",
  "name": "User Name"
}
Requirements:
Validate email.
Validate password policy.
Normalize email.
Prevent duplicate accounts.
Hash password using approved password hashing algorithm.
Never store plaintext passwords.
Create user identity.
Create initial account state.
Issue authentication credentials according to Section 15.
Generate audit event.
Login
POST /api/v1/auth/login
Requirements:
Rate limited.
Generic authentication errors.
No user enumeration.
Record security-relevant events.
Detect suspicious login behavior.
Return authenticated session information.
Refresh Token
POST /api/v1/auth/refresh
Requirements:
Validate refresh token.
Rotate refresh token where applicable.
Detect reuse.
Revoke compromised token families.
Never accept expired or revoked refresh tokens.
Logout
POST /api/v1/auth/logout
Requirements:
Revoke current session/token.
Idempotent operation.
Audit security event.
Password Reset
POST /api/v1/auth/forgot-password
POST /api/v1/auth/reset-password
Requirements:
Never reveal whether an email exists.
Reset tokens MUST expire.
Reset tokens MUST be single-use.
Existing sessions SHOULD be invalidated according to security policy.
Password reset events MUST be audited.
6.5 Standard Response Format
Successful single resource:
{
  "data": {},
  "meta": {}
}
Collection:
{
  "data": [],
  "meta": {
    "page": 1,
    "page_size": 20,
    "total": 100,
    "has_next": true
  }
}
Error:
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed.",
    "details": [
      {
        "field": "email",
        "code": "INVALID_EMAIL"
      }
    ],
    "request_id": "req_xxx"
  }
}
The client MUST use error.code for programmatic handling.
The client MUST NOT depend on the human-readable message.
6.6 HTTP Status Codes
Status
Meaning
200
Successful request
201
Resource created
202
Accepted asynchronous operation
204
Successful request without body
400
Invalid request
401
Authentication required/invalid
403
Authenticated but unauthorized
404
Resource not found
409
Conflict
422
Validation failure
429
Rate limit exceeded
500
Internal server error
502
Upstream dependency failure
503
Service temporarily unavailable
6.7 Pagination
Collections MUST support pagination.
Preferred parameters:
?page=1&page_size=20
Maximum page size MUST be enforced server-side.
Example:
?page=2&page_size=50
The server MUST reject excessive values rather than allowing uncontrolled database queries.
6.8 Filtering
Filtering MUST use explicitly supported parameters.
Example:
GET /api/v1/exercises?muscle_group=chest&difficulty=beginner
The API MUST NOT allow arbitrary SQL-like filtering expressions.
6.9 Sorting
Only predefined sortable fields may be accepted.
Example:
?sort=created_at
?sort=-created_at
The backend MUST maintain a whitelist.
6.10 Idempotency
Idempotency MUST be implemented for operations where duplicate execution can cause financial, subscription, notification, or state corruption.
Examples:
POST /payments
POST /subscriptions
POST /workout-sessions
POST /notification-delivery
Clients MAY send:
Idempotency-Key: <uuid>
The backend MUST prevent duplicate processing.
6.11 Workout API
Representative endpoints:
GET    /api/v1/workouts
GET    /api/v1/workouts/{workout_id}
POST   /api/v1/workouts/{workout_id}/start
POST   /api/v1/workout-sessions
PATCH  /api/v1/workout-sessions/{session_id}
POST   /api/v1/workout-sessions/{session_id}/complete
POST   /api/v1/workout-sessions/{session_id}/cancel
Workout execution MUST be validated against the current workout definition.
The client MUST NOT be allowed to modify protected workout-plan rules.
6.12 Exercise API
GET /api/v1/exercises
GET /api/v1/exercises/{exercise_id}
Admin:
POST   /api/v1/admin/exercises
PATCH  /api/v1/admin/exercises/{exercise_id}
DELETE /api/v1/admin/exercises/{exercise_id}
Exercise content MUST be validated before publication.
6.13 Nutrition API
GET /api/v1/nutrition/today
GET /api/v1/nutrition/plan
GET /api/v1/nutrition/meals
POST /api/v1/nutrition/meals/{meal_id}/replace
The client MUST NOT directly calculate authoritative nutrition targets.
Server-side calculation MUST be the source of truth.
6.14 AI Coach API
POST /api/v1/coach/conversations
GET  /api/v1/coach/conversations
GET  /api/v1/coach/conversations/{conversation_id}
POST /api/v1/coach/conversations/{conversation_id}/messages
GET  /api/v1/coach/insights
AI requests MUST:
authenticate the user;
enforce entitlement;
validate input;
apply safety rules;
retrieve only authorized user context;
prevent prompt injection from becoming system-level instructions;
enforce output schema where structured output is expected;
log appropriate metadata without storing unnecessary sensitive content.
6.15 Admin API
All administrative endpoints MUST be namespaced:
/api/v1/admin/*
Examples:
GET /api/v1/admin/users
GET /api/v1/admin/exercises
GET /api/v1/admin/workout-plans
GET /api/v1/admin/nutrition
GET /api/v1/admin/notifications
GET /api/v1/admin/subscriptions
GET /api/v1/admin/analytics
Every Admin endpoint MUST enforce:
Authentication.
Active session.
Admin role.
Required permission.
Resource-level authorization where applicable.
Audit logging.
6.16 API Security Requirements
Every protected endpoint MUST pass through:
Request
 ↓
HTTPS
 ↓
Request ID
 ↓
Rate Limit
 ↓
Authentication
 ↓
Authorization
 ↓
Input Validation
 ↓
Business Rules
 ↓
Database / Service
 ↓
Response Sanitization
 ↓
Audit / Metrics
No endpoint may bypass the security pipeline without an explicitly documented reason.
6.17 API Documentation
FastAPI OpenAPI documentation MUST be generated automatically.
Development:
/docs
/redoc
/openapi.json
Production exposure of interactive documentation MUST follow the production security policy and SHOULD be restricted or disabled where appropriate.
06.18 API Contract Rule
The API contract is authoritative.
Flutter and Admin developers MUST implement against the defined API schemas.
They MUST NOT infer backend behavior from the prototype.
07 — W8 Security Architecture, Threat Model & RBAC
7.1 Security Objective
W8 handles:
User identity.
Personal information.
Fitness data.
Nutrition information.
AI conversations.
Subscription information.
Administrative data.
Security MUST therefore be treated as a core architecture requirement rather than a post-development feature.
7.2 Security Principles
W8 MUST follow:
Zero Trust principles.
Least privilege.
Defense in depth.
Secure defaults.
Fail closed.
Explicit authorization.
Data minimization.
Separation of duties.
Auditability.
Secure dependency management.
7.3 Trust Boundaries
The system contains these major trust zones:
                    Internet
                       │
                ┌──────▼──────┐
                │ API Gateway │
                └──────┬──────┘
                       │
             ┌─────────▼─────────┐
             │ W8 Backend API    │
             └───────┬─────┬─────┘
                     │     │
          ┌──────────▼┐   ┌▼──────────┐
          │ PostgreSQL│   │   Redis   │
          └───────────┘   └───────────┘
                     │
                ┌────▼────┐
                │ Workers │
                └─────────┘
External services MUST NOT receive unrestricted access to internal resources.
7.4 Threat Model
The threat model MUST consider at minimum:
Account attacks
Credential stuffing.
Brute force.
Password spraying.
Session theft.
Refresh-token theft.
Session fixation.
Account enumeration.
API attacks
IDOR.
Broken access control.
Injection.
Mass assignment.
Parameter tampering.
Replay attacks.
Rate-limit bypass.
Mobile attacks
Token extraction.
Reverse engineering.
API endpoint discovery.
Modified application requests.
Certificate interception.
Local storage compromise.
Admin attacks
Privilege escalation.
Unauthorized content publishing.
Account takeover.
Destructive actions.
Insider misuse.
AI attacks
Prompt injection.
Data exfiltration through prompts.
Tool abuse.
Unauthorized user-context retrieval.
Model manipulation.
Unsafe recommendations.
Infrastructure attacks
Exposed secrets.
Vulnerable dependencies.
Container escape.
Misconfigured storage.
Public database access.
Excessive cloud permissions.
7.5 Authentication
Authentication requirements are defined in Section 15.
Security architecture MUST ensure:
Strong password hashing.
Secure token lifecycle.
Session revocation.
Refresh token rotation.
Account lock/rate limiting where appropriate.
Secure password reset.
Email verification where required.
7.6 Authorization
Authentication answers:
Who are you?
Authorization answers:
Are you allowed to perform this operation?
Every protected operation MUST perform authorization.
7.7 RBAC
Minimum roles:
USER
ADMIN
CONTENT_EDITOR
SUPPORT
ANALYST
SUPER_ADMIN
Role definitions:
USER
Can:
access own profile;
access own workouts;
access own nutrition;
access own AI conversations;
manage own preferences;
manage own subscription.
Cannot:
access another user's data;
access Admin APIs;
modify published content.
SUPPORT
Can:
view permitted user account information;
assist with support operations;
view relevant subscription/account status.
Cannot:
publish content;
modify security policies;
access unrestricted sensitive information.
CONTENT_EDITOR
Can:
create exercises;
edit exercises;
create workout plans;
manage nutrition content;
manage content drafts.
Publishing permissions SHOULD be separated where required.
ANALYST
Can:
access approved analytics;
view aggregated metrics.
Must NOT access unnecessary personal user data.
ADMIN
Can perform administrative operations according to assigned permissions.
SUPER_ADMIN
Reserved for the highest-trust administrative operations.
Usage MUST be minimized.
7.8 Permission Model
Roles MUST map to explicit permissions.
Example:
users.read
users.update
users.suspend

exercises.read
exercises.create
exercises.update
exercises.publish
exercises.delete

workouts.create
workouts.update
workouts.publish

nutrition.create
nutrition.update
nutrition.publish

notifications.create
notifications.send

subscriptions.read
subscriptions.manage

analytics.read

audit.read
Code MUST check permissions rather than scattering role-name checks throughout the application.
Bad:
if user.role == "admin":
Preferred:
require_permission("exercises.publish")
7.9 Object-Level Authorization
RBAC alone is insufficient.
A USER with permission to:
workout.read
must only read workouts belonging to that user.
The backend MUST prevent:
GET /workouts/{another_user_workout_id}
from returning unauthorized information.
This MUST be enforced at the backend/database query layer.
7.10 Secrets Management
Secrets MUST NOT exist in:
source code;
Git history;
Flutter source;
Admin frontend;
Docker images;
README files;
logs.
Examples:
DATABASE_URL
JWT_SECRET
REDIS_URL
AI_PROVIDER_KEY
PAYMENT_SECRET
WEBHOOK_SECRET
must be supplied through environment/secret-management infrastructure.
7.11 Sensitive Data Logging
Never log:
passwords;
authentication tokens;
refresh tokens;
payment secrets;
API keys;
full private AI conversations unless explicitly approved;
sensitive personal data unnecessarily.
Logs MUST use redaction.
7.12 Encryption
Data in transit:
HTTPS/TLS
Data at rest:
encrypted infrastructure;
encrypted backups;
secure object storage.
Encryption keys MUST be managed separately from encrypted data.
7.13 Rate Limiting
Rate limits MUST exist for:
Login.
Registration.
Password reset.
AI requests.
OTP/email verification if applicable.
Public endpoints.
Admin APIs.
Expensive queries.
AI endpoints MUST have stricter cost-aware limits.
7.14 AI Security
The AI layer MUST never be treated as an authority.
AI output:
UNTRUSTED MODEL OUTPUT
must pass through:
Validation
↓
Safety Rules
↓
Domain Constraints
↓
Response Schema
↓
User
AI MUST NOT directly:
modify database records;
change subscriptions;
grant entitlements;
modify workout plans;
perform privileged admin operations;
unless explicitly mediated by authorized backend tools.
7.15 Audit Logging
Security-sensitive actions MUST generate audit events.
Examples:
LOGIN
LOGOUT
PASSWORD_RESET
ROLE_CHANGED
USER_SUSPENDED
CONTENT_CREATED
CONTENT_PUBLISHED
CONTENT_DELETED
SUBSCRIPTION_CHANGED
ADMIN_ACTION
SECURITY_EVENT
Audit records MUST be append-oriented and protected against unauthorized modification.
7.16 Security Testing
CI MUST include appropriate:
dependency scanning;
secret scanning;
static analysis;
container scanning;
API authorization tests;
authentication tests;
input validation tests.
Recommended tools include:
Gitleaks
Trivy
Bandit
Ruff
pip-audit
Semgrep
Exact tooling may evolve, but security controls MUST remain.
08 — W8 Data & Storage Architecture
8.1 Storage Architecture
Primary storage:
PostgreSQL
Caching/session-support/temporary state:
Redis
Object/media storage:
S3-compatible object storage
Background jobs:
Celery + Redis
The exact production infrastructure may change, but application-level storage boundaries MUST remain.
8.2 PostgreSQL Responsibilities
PostgreSQL is the source of truth for:
Users.
Profiles.
Exercises.
Workout plans.
Workout sessions.
Nutrition plans.
Meals.
AI conversation metadata.
Notifications.
Subscriptions.
Entitlements.
Audit records.
Administrative data.
8.3 Redis Responsibilities
Redis MAY be used for:
caching;
rate limiting;
temporary authentication state;
distributed locks;
background task broker;
short-lived AI state;
idempotency records.
Redis MUST NOT become the authoritative database.
Critical persistent data MUST NOT exist only in Redis.
8.4 Object Storage
Object storage MUST be used for:
exercise images;
exercise videos where applicable;
user-uploaded media if introduced;
generated media;
exported reports;
other large binary assets.
Database rows SHOULD contain metadata and object references rather than large binary payloads.
8.5 Database Principles
The database MUST use:
UUID primary identifiers where appropriate;
foreign keys;
unique constraints;
check constraints where useful;
indexes;
timestamps;
soft deletion where business requirements require it;
explicit status fields;
migrations.
8.6 Timestamps
All persisted timestamps MUST use UTC.
Example:
2026-08-20T12:30:00Z
User-local timezone is presentation metadata.
The backend MUST NOT store local timestamps as authoritative event timestamps.
8.7 Audit Fields
Entities that require lifecycle tracking SHOULD contain:
created_at
updated_at
created_by
updated_by
Where applicable:
published_at
published_by
deleted_at
deleted_by
8.8 Content Versioning
Content such as:
exercises;
workout plans;
nutrition plans;
MUST support controlled lifecycle states.
Recommended:
DRAFT
IN_REVIEW
PUBLISHED
ARCHIVED
Published content MUST NOT be silently modified in a way that changes historical user records.
8.9 Workout Data Integrity
When a user starts a workout, the application MUST preserve enough information to reconstruct what the user actually performed.
Historical workout records MUST NOT change simply because an Admin later edits the current workout plan.
This requires versioning/snapshotting strategy.
8.10 Nutrition Data Integrity
Nutrition plans must maintain historical consistency.
Changes to published nutrition definitions MUST NOT unexpectedly rewrite historical user records.
8.11 Indexing
Indexes MUST be created based on actual query patterns.
Likely indexed fields:
users.email
users.status
users.created_at

exercises.status
exercises.muscle_group
exercises.difficulty

workout_plans.status
workout_plans.created_at

workout_sessions.user_id
workout_sessions.started_at

notifications.user_id
notifications.created_at

subscriptions.user_id
subscriptions.status
Indexes MUST be reviewed as data volume grows.
8.12 Transactions
Operations involving multiple related records MUST use database transactions.
Example:
Complete workout
↓
Update workout session
↓
Update progress metrics
↓
Generate relevant event
If atomicity is required, these operations MUST be coordinated safely.
8.13 Concurrency
The backend MUST consider:
duplicate requests;
simultaneous updates;
retry behavior;
race conditions;
background workers;
webhook duplication.
Use:
transactions;
unique constraints;
optimistic locking where appropriate;
idempotency;
distributed locks only where justified.
8.14 Database Migrations
Alembic MUST be used.
Rules:
Every schema change requires a migration.
Migrations MUST be version-controlled.
Migrations MUST be reversible where practical.
Destructive migrations require explicit review.
Production migrations MUST be tested against realistic data.
Application deployment MUST remain compatible with migration sequencing.
Never manually modify production schema as a normal workflow.
8.15 Backups
Production database MUST have:
automated backups;
retention policy;
backup monitoring;
restore testing.
A backup that has never been restored successfully MUST NOT be considered reliable.
8.16 Data Retention
Retention policies MUST be defined for:
application data;
AI conversations;
logs;
audit events;
deleted accounts;
backups;
analytics.
Data MUST NOT be retained indefinitely without a business/legal reason.
8.17 User Deletion
Account deletion MUST distinguish:
Account deletion
from:
Physical database deletion
The system MUST determine which records:
must be deleted;
must be anonymized;
must be retained for legal/audit requirements.
This policy MUST be implemented consistently across PostgreSQL, Redis, object storage, analytics, and AI-related storage.
08.18 Data Access Rule
No Flutter/Admin client may receive database credentials.
No client may directly query PostgreSQL or Redis.
09 — W8 Backend Project Structure & Coding Standards
9.1 Backend Stack
The backend SHOULD use:
Python
FastAPI
SQLAlchemy
Alembic
PostgreSQL
Redis
Celery
Pydantic
Pytest
Supporting tooling:
Ruff
MyPy
Pre-commit
Gitleaks
Trivy
Exact versions MUST be pinned and maintained.
9.2 Backend Directory Structure
Recommended:
apps/api/
├── app/
│   ├── main.py
│   │
│   ├── api/
│   │   ├── deps.py
│   │   ├── router.py
│   │   └── v1/
│   │       ├── auth.py
│   │       ├── users.py
│   │       ├── profile.py
│   │       ├── onboarding.py
│   │       ├── exercises.py
│   │       ├── workouts.py
│   │       ├── nutrition.py
│   │       ├── coach.py
│   │       ├── notifications.py
│   │       ├── subscriptions.py
│   │       └── admin/
│   │
│   ├── core/
│   │   ├── config.py
│   │   ├── security.py
│   │   ├── logging.py
│   │   ├── errors.py
│   │   └── telemetry.py
│   │
│   ├── db/
│   │   ├── session.py
│   │   ├── base.py
│   │   └── models/
│   │
│   ├── schemas/
│   │
│   ├── services/
│   │
│   ├── repositories/
│   │
│   ├── domain/
│   │
│   ├── workers/
│   │
│   └── integrations/
│
├── migrations/
├── tests/
├── pyproject.toml
├── Dockerfile
└── README.md
9.3 Architectural Layers
The backend MUST separate:
API
 ↓
Application Service
 ↓
Domain
 ↓
Repository
 ↓
Database
Example:
HTTP Request
    ↓
Router
    ↓
Pydantic Schema
    ↓
Application Service
    ↓
Business Rules
    ↓
Repository
    ↓
SQLAlchemy
    ↓
PostgreSQL
Routers MUST NOT contain complex business logic.
9.4 API Layer
Routers are responsible for:
HTTP concerns;
authentication dependencies;
request parsing;
response serialization;
status codes.
Routers MUST NOT:
perform large SQL queries directly;
contain workout business rules;
implement nutrition calculations;
call external providers directly unless through an integration service.
9.5 Service Layer
Services contain application behavior.
Example:
WorkoutService
NutritionService
SubscriptionService
NotificationService
CoachService
ContentService
Services coordinate:
domain rules;
repositories;
external integrations;
transactions.
9.6 Repository Layer
Repositories are responsible for data access.
Example:
UserRepository
ExerciseRepository
WorkoutRepository
NutritionRepository
SubscriptionRepository
Repositories MUST NOT contain API-specific behavior.
9.7 Schemas
Pydantic schemas MUST distinguish:
Create
Update
Read
Internal
Example:
ExerciseCreate
ExerciseUpdate
ExerciseResponse
ExerciseInternal
Database ORM models MUST NOT automatically become public API schemas.
9.8 Dependency Injection
FastAPI dependency injection MUST be used for:
database sessions;
current user;
permissions;
services;
request context.
Avoid global mutable state.
9.9 Configuration
Configuration MUST be environment-driven.
Example:
DATABASE_URL
REDIS_URL
JWT_SECRET
ENVIRONMENT
LOG_LEVEL
AI_PROVIDER
Use typed settings.
Never:
SECRET = "abc123"
9.10 Error Handling
Use centralized exception handling.
Application errors MUST have stable error codes.
Example:
USER_NOT_FOUND
EXERCISE_NOT_FOUND
INSUFFICIENT_ENTITLEMENT
FORBIDDEN
INVALID_WORKOUT_STATE
CONTENT_NOT_PUBLISHED
RATE_LIMITED
Internal stack traces MUST NOT be exposed to clients.
9.11 Logging
Use structured logging.
Example:
{
  "timestamp": "...",
  "level": "INFO",
  "request_id": "...",
  "event": "workout_completed",
  "user_id": "..."
}
Logs MUST be machine-readable.
9.12 Type Safety
Python code MUST use type hints.
Example:
async def get_exercise(
    exercise_id: UUID,
) -> ExerciseResponse:
    ...
Avoid:
def get_data(x):
9.13 Async Rules
Async code MUST remain genuinely asynchronous.
Do not execute blocking operations inside async request handlers without proper isolation.
External calls and heavy work SHOULD be moved to appropriate services/workers.
9.14 Background Jobs
Celery workers SHOULD handle:
email;
push notifications;
AI generation;
report generation;
scheduled jobs;
analytics aggregation;
expensive processing.
Request handlers MUST NOT wait unnecessarily for long-running jobs.
9.15 Testing
Backend tests MUST include:
Unit tests
Business rules.
Integration tests
Database/repository behavior.
API tests
HTTP contract.
Security tests
Authentication/authorization.
Regression tests
Previously fixed bugs.
9.16 Coding Rules
The AI MUST NOT:
duplicate business logic;
introduce unnecessary abstractions;
create giant files;
bypass repository/service architecture;
access environment secrets directly throughout the codebase;
silently catch exceptions;
disable lint/security tools to make CI pass;
weaken tests to make implementation pass.
9.17 Pull Request Quality
Every meaningful backend change SHOULD contain:
Implementation
Tests
Migration if required
Documentation if required
Security consideration
09.18 Backend Definition
Backend code is considered complete only when:
Code
+
Tests
+
Validation
+
Security
+
Observability
+
Documentation
are all addressed.
10 — W8 Flutter Architecture & Coding Standards
10.1 Objective
The Flutter application MUST reproduce the approved W8 prototype experience while replacing prototype limitations with production-grade:
architecture;
state management;
networking;
persistence;
accessibility;
localization;
responsive layouts;
error handling;
security;
testing.
The docs/design/index.html prototype is a visual and interaction reference, not an implementation source.
The Master Specification remains authoritative for behavior.
10.2 Flutter Architecture
Recommended architecture:
Presentation
     ↓
Application / State
     ↓
Domain
     ↓
Data
     ↓
API / Local Storage
Recommended state management:
Riverpod
or another explicitly approved architecture.
The implementation MUST use one consistent state-management approach.
10.3 Flutter Directory Structure
apps/mobile/
├── lib/
│   ├── main.dart
│   │
│   ├── app/
│   │   ├── app.dart
│   │   ├── router.dart
│   │   ├── theme/
│   │   ├── localization/
│   │   └── config/
│   │
│   ├── core/
│   │   ├── network/
│   │   ├── storage/
│   │   ├── errors/
│   │   ├── security/
│   │   ├── analytics/
│   │   ├── accessibility/
│   │   └── utils/
│   │
│   ├── shared/
│   │   ├── widgets/
│   │   ├── components/
│   │   └── models/
│   │
│   └── features/
│       ├── auth/
│       ├── onboarding/
│       ├── dashboard/
│       ├── workouts/
│       ├── nutrition/
│       ├── coach/
│       ├── history/
│       ├── notifications/
│       ├── profile/
│       ├── settings/
│       └── subscription/
│
├── test/
├── integration_test/
├── assets/
└── pubspec.yaml
10.4 Feature-Based Organization
Each feature SHOULD follow:
feature/
├── data/
├── domain/
└── presentation/
Example:
features/workouts/
├── data/
│   ├── datasources/
│   ├── models/
│   └── repositories/
│
├── domain/
│   ├── entities/
│   ├── repositories/
│   └── usecases/
│
└── presentation/
    ├── providers/
    ├── screens/
    └── widgets/
This prevents the application from becoming a collection of unrelated screens.
10.5 Navigation
Use a centralized router.
Recommended:
go_router
Routes MUST be defined centrally.
Authentication state MUST control protected routes.
Example:
/onboarding
/login
/dashboard
/workouts
/nutrition
/coach
/history
/settings
/notifications
10.6 Route Guards
Protected routes MUST verify authentication.
Premium routes MUST verify entitlement where required.
Example:
Route
 ↓
Authenticated?
 ↓
Authorized?
 ↓
Entitled?
 ↓
Screen
The UI may hide locked content, but backend authorization remains authoritative.
10.7 Networking
Use a dedicated HTTP client layer.
Recommended:
Dio
Responsibilities:
base URL;
authentication headers;
token refresh;
timeout;
retry policy;
request IDs;
error mapping;
interceptors.
Widgets MUST NOT directly call HTTP APIs.
Bad:
onPressed: () async {
  await dio.get(...);
}
Preferred:
Widget
 ↓
Provider
 ↓
Repository
 ↓
API Client
10.8 API Models
API models MUST be explicitly defined.
Example:
UserDto
WorkoutDto
ExerciseDto
NutritionPlanDto
CoachMessageDto
NotificationDto
SubscriptionDto
Do not expose raw JSON throughout the UI.
10.9 State Management
Each asynchronous feature MUST represent:
initial
loading
data
empty
error
Where applicable:
refreshing
submitting
success
Example conceptual state:
AsyncValue<T>
The UI MUST explicitly handle all expected states.
10.10 Loading States
Never display an empty screen while data is loading.
Use:
skeleton;
progress indicator;
placeholder;
contextual loading state.
Avoid unnecessary full-screen spinners.
10.11 Error States
Every network-backed screen MUST have an error state.
The error state MUST provide an appropriate recovery action.
Examples:
Retry
Try again
Refresh
Go back
Errors MUST be user-friendly.
Technical exception messages MUST NOT be shown directly.
10.12 Offline Behavior
The app MUST distinguish:
No Internet
Server unavailable
Authentication expired
Empty data
These are not the same state.
Offline-capable screens SHOULD display cached data where safe and appropriate.
10.13 Local Storage
Local storage MAY be used for:
onboarding progress;
theme preference;
locale;
cached content;
non-sensitive app settings.
Sensitive authentication data MUST use secure platform storage.
Recommended:
flutter_secure_storage
Do not store secrets in:
SharedPreferences
10.14 Design System
All screens MUST use the W8 design system.
Required reusable components include:
W8Button
W8Card
W8IconButton
W8Chip
W8Switch
W8SegmentedControl
W8ProgressRing
W8ProgressBar
W8ListTile
W8StatusRow
W8BottomSheet
W8ConfirmDialog
W8StateBlock
W8TextField
Exact naming may be finalized during implementation, but duplication of equivalent components is prohibited.
10.15 Theme
The app MUST support:
Light
Dark
and MUST be structured to support:
System
if enabled by product requirements.
No screen may contain raw production colors.
Bad:
Color(0xFFFF5C00)
Preferred:
W8Theme.of(context).colors.accent
10.16 Typography
Typography MUST use the centralized W8 typography system.
No screen-specific arbitrary font sizes unless explicitly approved.
The font decision identified in the prototype audit MUST be resolved before final visual implementation:
Somar Sans
must either be properly licensed/bundled or replaced deliberately by the approved fallback/typeface.
It MUST NOT remain an accidental fallback.
10.17 Localization
Use:
flutter_localizations
ARB
generated AppLocalizations
Supported locales:
en
ar
Every user-visible string MUST be localized.
This includes:
Notifications;
Admin where applicable;
errors;
validation;
accessibility labels;
empty states;
loading messages.
No hardcoded UI strings are permitted.
10.18 RTL
Arabic MUST use Flutter's native directionality.
Do not manually reverse icons or paddings unless the semantic meaning of the icon requires it.
Use logical layout primitives.
Test every major screen in:
LTR
RTL
10.19 Forms
All real forms MUST use actual Flutter form controls:
TextField
TextFormField
Dropdown
Checkbox
Switch
ChoiceChip
Never simulate form controls using:
Container
GestureDetector
Text
10.20 Accessibility
Every interactive element MUST have:
semantic meaning;
focus support;
accessible label where required;
minimum tap target;
appropriate enabled/disabled state.
Minimum target:
44 × 44 logical pixels
Icon-only controls MUST have:
Tooltip
and/or:
Semantics(label: ...)
10.21 Charts
Charts MUST expose accessible information.
A user who cannot visually interpret a chart MUST still be able to understand its important values.
Charts MUST provide:
label
value
period
through semantic accessibility.
10.22 AI Coach UI
The composer MUST be a real:
TextField
The feature MUST support:
typing
sending
loading
streaming if implemented
error
retry
empty conversation
The UI MUST never assume AI responses are instantaneous.
10.23 Keyboard Handling
AI Coach and forms MUST use proper keyboard avoidance.
The implementation MUST account for:
Android keyboard;
iOS keyboard;
safe areas;
scrolling;
focused field visibility.
10.24 Deep Links
The architecture SHOULD support deep links for future flows such as:
notification → workout
notification → subscription
notification → nutrition
notification → coach
Deep-link authorization MUST still be enforced.
10.25 Secure Client Principle
Flutter is an untrusted client.
Never trust:
isAdmin
isPremium
price
permissions
workout completion
nutrition totals
coming from the client.
These values may be displayed by the client but authoritative decisions MUST come from the backend.
10.26 Performance
Flutter implementation MUST avoid:
unnecessary rebuilds;
giant widget trees;
unbounded lists;
loading full image assets unnecessarily;
blocking the UI isolate;
expensive synchronous parsing.
Use:
ListView.builder
Slivers
pagination
image caching
isolates/background processing
where appropriate.
10.27 Image & Media Handling
Exercise media MUST support:
lazy loading;
caching;
placeholders;
error state;
appropriate resolution;
memory-conscious decoding.
The app MUST NOT package the entire exercise media library into the binary unless explicitly required.
10.28 Testing
Flutter tests MUST include:
Unit
business logic;
formatters;
state transitions.
Widget
UI states;
interactions;
localization;
accessibility.
Integration
Critical flows:
Registration
Login
Onboarding
Workout
Nutrition
AI Coach
Subscription
Notifications
Logout
10.29 Flutter Coding Standards
The AI MUST:
keep widgets focused;
extract reusable components;
avoid giant build() methods;
use immutable state where possible;
use typed models;
avoid dynamic data propagation;
avoid duplicated API calls;
avoid business logic inside widgets;
avoid hardcoded strings;
avoid hardcoded colors;
avoid hardcoded API URLs;
avoid storing secrets.
10.30 Prototype Implementation Rule
The docs/design/index.html file is a reference artifact.
It may be inspected to understand:
spacing;
visual hierarchy;
interactions;
screen composition;
states;
navigation;
visual details.
But AI MUST NOT blindly convert HTML/CSS into Flutter.
The correct process is:
Prototype
    ↓
Master Specification
    ↓
Design System
    ↓
Flutter Architecture
    ↓
Production Implementation
If prototype behavior conflicts with the Master Specification:
Master Specification wins.
10.31 Flutter Completion Rule
A Flutter feature is not complete merely because the screen visually matches the prototype.
It is complete only when:
UI
+
State
+
API Integration
+
Error Handling
+
Loading/Empty States
+
Localization
+
RTL
+
Accessibility
+
Security
+
Tests
are implemented according to the applicable specification.
10.32 Cross-Section Architectural Rule
The following hierarchy is mandatory throughout W8:
W8_MASTER_TECHNICAL_SPECIFICATION.md
             │
             ├── Product requirements
             ├── Domain rules
             ├── API contracts
             ├── Security requirements
             ├── Data architecture
             └── UI/UX requirements
                       │
                       ▼
                    AGENTS.md
                       │
                       ├── AI coding behavior
                       ├── Engineering workflow
                       ├── Safety rules
                       ├── Validation rules
                       └── Definition of acceptable changes
                              │
                              ▼
                       Implementation
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
           Flutter           API            Admin
              │               │               │
              └───────────────┼───────────────┘
                              ▼
                           Tests
                              │
                              ▼
                             CI
                              │
                              ▼
                         Staging
                              │
                              ▼
                         Production
No AI agent is allowed to treat generated code as authoritative over the specification.
The specification is authoritative; implementation is disposable and must be corrected whenever it diverges from the specification.
Important قبل ما نكمل Section 11
أنا شايف إننا وصلنا لنقطة مهمة جدا في بناء الوثيقة. من هنا فصاعدا، مش عايزين نكتب Sections لمجرد إننا نوصل لـ29.
الـSections اللي بعد كده لازم تبني على الـ6–10 دول، خصوصا:
11 — Admin Dashboard
12 — Content Management
13 — AI Coach
14 — Notifications
15 — Authentication
16 — Payments
17 — Observability
18 — Testing / QA
19 — CI/CD
20 — Performance
...
والأهم إننا أثناء كتابة كل Section نمنع التعارض بين الأقسام.
الهيكل النهائي اللي أنصحك تحافظ عليه
W8_MASTER_TECHNICAL_SPECIFICATION.md

01 — Product Vision & Engineering Principles
02 — Product Scope & Complete Feature Specification
03 — W8 Domain & Data Model
04 — W8 Business Rules & Content Specification
05 — W8 System Architecture & Technical Architecture
06 — W8 API Contract & Backend Specification
07 — W8 Security Architecture, Threat Model & RBAC
08 — W8 Data & Storage Architecture
09 — W8 Backend Project Structure & Coding Standards
10 — W8 Flutter Architecture & Coding Standards
11 — W8 Admin Dashboard Architecture & Specification
12 — W8 Content Management...
13 — W8 AI Coach...
14 — W8 Notifications...
15 — W8 Authentication...
16 — W8 Payments...
17 — W8 Observability...
18 — W8 Testing & QA...
19 — W8 CI/CD...
20 — W8 Performance...
21 — W8 Infrastructure...
22 — W8 Deployment...
23 — W8 Disaster Recovery...
24 — W8 Privacy & Compliance...
25 — W8 Analytics...
26 — W8 Final Product/Engineering Constraints
27 — AI Development Rules
28 — Definition of Done
29 — Implementation Milestones
نقطة مهمة جدا: ملفات M0...M19 اللي أنت عاملها مش بديل للـMaster Specification. هي Implementation Layer مبنية عليها:
MASTER SPEC
    ↓
AGENTS.md
    ↓
M0–M19
    ↓
Tasks
    ↓
AI implementation
    ↓
Tests
    ↓
Review
وبالتالي لما نخلص الـ29 Section، نقدر نستخدمهم فعليا كـ source of truth للـAI بدل ما تديله الشات القديم كله.
