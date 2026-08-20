تمام. هنكمل بنفس فلسفة الـ Master Specification: الوثيقة تحدد WHAT + WHY + CONTRACTS + CONSTRAINTS، بينما AGENTS.md يحدد HOW الـAI ينفذ.
والأهم: بما إنك هتجمع كل Section في ملف واحد، هكتبهم بصياغة جاهزة للنسخ مباشرة، بدون كلام خارج الوثيقة.
11 — W8 Admin Dashboard Architecture
11.1 Purpose
The W8 Admin Dashboard is the internal operational control surface for managing the W8 platform.
It is responsible for:
User management.
Exercise library management.
Workout plan management.
Nutrition content management.
AI configuration and monitoring.
Notification management.
Subscription and entitlement operations.
Analytics.
Audit visibility.
Platform configuration.
Content publishing workflows.
The Admin Dashboard is an internal privileged application and must never be treated as an extension of the mobile application's trust boundary.
All administrative operations must be authenticated, authorized, validated, audited, and server-enforced.
The frontend must never be trusted to enforce administrative permissions.
11.2 Admin Architecture
The Admin system consists of:
Admin Browser
     │
     ▼
Admin Flutter/Web Application
     │
     ▼
Authenticated API
     │
     ├── RBAC / Authorization
     ├── Business Rules
     ├── Audit Logging
     ├── Validation
     └── Domain Services
            │
            ├── PostgreSQL
            ├── Redis
            ├── Object Storage
            ├── AI Services
            └── Notification Providers
The Admin application must communicate with the backend exclusively through authenticated API contracts.
Direct database access from the Admin frontend is forbidden.
11.3 Admin Sections
The Admin Dashboard shall contain the following primary sections:
Dashboard
Users
Exercises
Workout Plans
Nutrition
AI Coach
Notifications
Subscriptions
Analytics
Settings
Audit Logs
Additional sections may be introduced later without changing the authorization model.
11.4 Dashboard
The Admin Dashboard provides operational visibility.
Required metrics
At minimum:
Total users.
Active users.
New users.
Users active today.
Workout completions.
Nutrition activity.
AI Coach usage.
Subscription count.
Active subscriptions.
Trial users.
Revenue metrics where payment integration supports them.
Notification delivery metrics.
API error rate.
Critical system health indicators.
Metrics must specify:
Current value.
Time period.
Previous-period comparison where applicable.
Timestamp of data freshness.
Example:
Active Users
12,482
+8.4% vs previous 7 days
Updated 3 minutes ago
11.5 Users Management
Admin users must be able to:
Search users.
Filter users.
View user summary.
View account status.
View subscription state.
View onboarding state.
View activity summary.
View consent state.
View notification preferences.
Suspend account.
Reactivate account.
Force logout where supported.
Revoke sessions.
Trigger password/account recovery workflows where permitted.
Delete/anonymize account according to retention policy.
Sensitive information must be minimized.
The Admin UI must not expose:
Passwords.
Authentication secrets.
Access tokens.
Refresh tokens.
Raw payment credentials.
Sensitive security secrets.
11.6 User Suspension
Suspension must be server-enforced.
Possible states:
ACTIVE
SUSPENDED
DELETED
PENDING_DELETION
A suspended user must not be able to perform authenticated application operations except explicitly permitted recovery actions.
Suspension must create an audit event containing:
actor_id
target_user_id
reason
timestamp
request_id
11.7 Exercise Management
Administrators/content managers must be able to:
Create exercise.
Edit exercise.
Archive exercise.
Restore exercise.
Search exercises.
Filter by muscle group.
Filter by equipment.
Filter by difficulty.
Filter by movement pattern.
Manage exercise media.
Manage instructions.
Manage safety notes.
Manage contraindication information.
Manage localized content.
Preview exercise.
Exercises should support lifecycle states:
DRAFT
IN_REVIEW
PUBLISHED
ARCHIVED
Only PUBLISHED exercises may be exposed to production users.
11.8 Workout Plan Management
Admins must be able to:
Create plans.
Duplicate plans.
Edit plans.
Configure training days.
Configure exercises.
Configure sets.
Configure repetitions.
Configure duration.
Configure rest periods.
Configure progression rules.
Configure difficulty.
Associate plans with goals.
Associate plans with experience levels.
Preview plans.
Publish plans.
Archive plans.
A plan must not be published unless all required validation rules pass.
11.9 Nutrition Management
The Nutrition CMS must support:
Foods.
Meals.
Recipes.
Meal plans.
Nutrition targets.
Serving sizes.
Macro information.
Dietary preferences.
Allergen information.
Localized descriptions.
Images.
Publishing state.
Nutrition content must be versioned.
Published content must not be silently mutated in a way that changes historical user records.
11.10 AI Coach Administration
The Admin Dashboard must provide operational controls for AI functionality.
It may include:
AI provider configuration.
Model configuration.
Prompt version management.
Safety policy configuration.
Feature flags.
Usage metrics.
Token usage.
Error rate.
Latency.
AI request volume.
Moderation events.
Failed generations.
Secrets such as API keys must never be displayed after initial configuration.
AI prompts and model configurations must be versioned.
11.11 Notification Management
Admin users must be able to:
Create notification campaigns.
Define audience.
Define title/body.
Localize content.
Schedule notifications.
Cancel scheduled notifications.
Preview notification.
View delivery status.
View failure status.
View engagement metrics where available.
Campaign lifecycle:
DRAFT
SCHEDULED
PROCESSING
SENT
CANCELLED
FAILED
11.12 Subscription Management
Admins may view:
Subscription status.
Product/plan.
Billing provider state.
Trial state.
Renewal state.
Cancellation state.
Entitlements.
Manual entitlement modification must be heavily restricted and audited.
The Admin Dashboard must never become a replacement for the payment provider's source of truth.
11.13 Analytics
Analytics must distinguish between:
Operational metrics
Product analytics
Business analytics
Technical metrics
Admin analytics must support date ranges and appropriate aggregation.
Analytics queries must not compromise transactional database performance.
Heavy analytics should use:
Aggregated tables.
Materialized views.
Analytics storage.
Asynchronous jobs.
11.14 Admin Settings
Settings may include:
Feature flags.
Application configuration.
AI configuration.
Notification configuration.
Localization configuration.
Security configuration.
Maintenance mode.
System limits.
Dangerous configuration changes must require:
Authorization.
Validation.
Confirmation.
Audit logging.
11.15 Admin Authorization
Admin permissions must be server-enforced.
Example roles:
SUPER_ADMIN
ADMIN
CONTENT_MANAGER
SUPPORT_AGENT
ANALYST
AI_MANAGER
Permissions should follow:
resource.action
Examples:
users.read
users.suspend
users.delete

exercises.read
exercises.create
exercises.update
exercises.publish

workouts.read
workouts.create
workouts.publish

nutrition.read
nutrition.create
nutrition.publish

notifications.create
notifications.send

analytics.read

audit_logs.read
Default permission is deny.
11.16 Admin Audit Requirements
Every privileged mutation must generate an audit record.
Minimum:
audit_id
actor_id
actor_role
action
resource_type
resource_id
timestamp
request_id
ip_hash_or_allowed_metadata
result
reason
metadata
Audit records must be append-only from the application's normal operational interface.
11.17 Admin State Handling
Every Admin data screen must explicitly support:
Loading
Success
Empty
Error
Unauthorized
Forbidden
Offline
Retrying
No screen may assume that data always exists.
11.18 Admin Pagination
All potentially large collections must use server-side pagination.
Example:
GET /admin/users?page=1&page_size=50
Maximum page size must be enforced by the backend.
The frontend must not request unlimited datasets.
11.19 Admin Search
Search must:
Use real input fields.
Debounce requests.
Validate query length.
Support cancellation of stale requests.
Use server-side filtering where appropriate.
Search must never construct SQL directly from user input.
11.20 Admin Confirmation
Destructive operations require explicit confirmation.
Examples:
Suspend user
Delete user
Archive exercise
Delete draft
Cancel campaign
Modify critical configuration
Revoke access
Confirmation must explain the consequence.
12 — W8 Content Management & Content Operations
12.1 Purpose
W8 content is a core business asset.
Content must not be embedded directly inside Flutter application code.
The platform must use a centralized Content Management System.
Primary content domains:
Exercises
Workout Plans
Workout Templates
Nutrition
Meals
Recipes
Foods
Educational Articles
AI Knowledge
12.2 Content Principles
All content must be:
Structured.
Versioned.
Localizable.
Validated.
Searchable.
Auditable.
Publishable.
Archivable.
Reversible where technically possible.
Production mobile clients consume published content through APIs.
12.3 Content Lifecycle
Standard lifecycle:
DRAFT
   ↓
IN_REVIEW
   ↓
APPROVED
   ↓
PUBLISHED
   ↓
ARCHIVED
Rejected content:
IN_REVIEW → CHANGES_REQUESTED
Only authorized roles may transition content between states.
12.4 Content Versioning
Published content must be immutable from the perspective of historical records.
If an exercise changes:
Exercise v1 → published
Exercise v2 → draft
Exercise v2 → review
Exercise v2 → published
Historical workout records referencing v1 must continue to resolve correctly.
12.5 Exercise Content Model
Exercise metadata should support:
id
slug
localized_name
localized_description
instructions
muscle_groups
secondary_muscles
equipment
movement_pattern
difficulty
exercise_type
instructions_by_step
common_mistakes
safety_notes
contraindications
media
video
thumbnail
status
version
created_at
updated_at
published_at
12.6 Exercise Validation
Before publication:
Name must exist in all required locales.
Instructions must exist.
Exercise category must be valid.
Difficulty must be valid.
Media references must resolve.
Required safety metadata must exist where applicable.
No broken relationships may exist.
Duplicate slug must be rejected.
Unsupported media must be rejected.
12.7 Workout Plan Content
A workout plan contains:
Plan
 ├── Goal
 ├── Level
 ├── Schedule
 ├── Days
 │    ├── Warmup
 │    ├── Exercises
 │    │    ├── Sets
 │    │    ├── Reps
 │    │    ├── Weight
 │    │    ├── Rest
 │    │    └── Tempo
 │    └── Cooldown
 └── Progression Rules
The CMS must allow content managers to construct this hierarchy without editing database records manually.
12.8 Workout Builder
Workout Builder must support:
Drag/reorder.
Exercise selection.
Set configuration.
Repetition configuration.
Time-based exercises.
Rest configuration.
Warm-up.
Cool-down.
Supersets where supported.
Notes.
Progression rules.
Optional exercises.
Exercise substitution rules.
Builder validation must run before saving and publishing.
12.9 Nutrition Builder
Nutrition Builder must support:
Food
Meal
Recipe
Meal Plan
Nutrition Target
Example:
Nutrition Plan
 ├── Breakfast
 │    ├── Food
 │    └── Recipe
 ├── Lunch
 ├── Snack
 └── Dinner
Each item should support serving size and nutrition values.
12.10 Nutrition Safety
Nutrition content must not be presented as individualized medical advice.
The system must distinguish between:
General nutrition guidance
Personalized fitness nutrition
Medical/dietary restrictions
High-risk conditions or medically sensitive situations should trigger appropriate safety messaging and avoid unsupported recommendations.
12.11 Content Localization
All user-facing content must support localization.
Minimum:
English
Arabic
Content should not be stored as concatenated UI strings.
Use structured localized fields.
Example:
{
  "en": "...",
  "ar": "..."
}
12.12 Media Management
Media must use object storage/CDN architecture.
Database records should contain references rather than binary media.
Example:
media_id
storage_key
mime_type
width
height
duration
checksum
status
Upload pipeline:
Admin
 ↓
Upload
 ↓
Validation
 ↓
Malware/content checks
 ↓
Object Storage
 ↓
CDN
 ↓
Published Content
12.13 Content Import
Bulk data import must be supported for the user's future exercise/workout/nutrition datasets.
Import process:
Upload
 ↓
Schema Validation
 ↓
Normalization
 ↓
Duplicate Detection
 ↓
Validation Report
 ↓
Draft Import
 ↓
Review
 ↓
Publish
Imported data must never automatically become production content without validation.
12.14 Content Audit
Every important content mutation must record:
who
what
when
before
after
version
reason
13 — W8 AI Coach & Personalization
13.1 Purpose
W8 AI Coach provides conversational and personalized fitness assistance while remaining constrained by W8's product rules, content, safety policies, and user context.
AI is an assistive system, not an unrestricted authority.
13.2 AI Architecture
Mobile
  ↓
W8 API
  ↓
AI Orchestrator
  ├── Authentication Context
  ├── User Profile
  ├── Workout Context
  ├── Nutrition Context
  ├── Content Retrieval
  ├── Safety Layer
  ├── Prompt Builder
  ├── Model Provider
  ├── Output Validation
  └── Response Formatter
The mobile application must never directly call a third-party AI provider using a provider API key.
13.3 AI Orchestrator
The AI Orchestrator is responsible for:
Context construction.
Prompt selection.
Model selection.
Retrieval.
Safety checks.
Token limits.
Rate limits.
Output validation.
Logging.
Cost tracking.
Fallback behavior.
13.4 AI Context
AI context may include:
User goals
Fitness level
Training schedule
Workout history
Exercise history
Nutrition preferences
Available equipment
Language
Relevant W8 content
Current workout
Current nutrition plan
Recent conversations
Only information necessary for the request should be sent to the model.
13.5 Context Boundaries
The AI must not receive unrestricted database access.
It must receive controlled context objects.
Example:
UserContext
WorkoutContext
NutritionContext
ContentContext
ConversationContext
13.6 Retrieval-Augmented Generation
When factual W8 content is required, the system should prefer W8's controlled content source over model memory.
Example:
Question
 ↓
Intent Detection
 ↓
Relevant W8 Content Retrieval
 ↓
Context Assembly
 ↓
LLM
 ↓
Safety Validation
 ↓
Response
The AI must not invent W8 exercises, plans, nutrition rules, or product policies when authoritative content is available.
13.7 AI Response Categories
The AI should distinguish:
GENERAL_FITNESS
WORKOUT_GUIDANCE
EXERCISE_GUIDANCE
NUTRITION_GUIDANCE
PLAN_EXPLANATION
PROGRESS_ANALYSIS
MOTIVATION
PRODUCT_HELP
SAFETY_RELATED
UNSUPPORTED
13.8 Safety Layer
AI responses must be checked for unsafe recommendations.
The AI must not:
Diagnose medical conditions.
Claim medical certainty.
Recommend dangerous training behavior.
Encourage exercise through serious injury.
Provide unsafe extreme dieting instructions.
Fabricate medical facts.
Claim to replace healthcare professionals.
For safety-sensitive requests, the system should respond conservatively and encourage appropriate professional assistance.
13.9 Prompt Management
Prompts must be versioned.
Example:
coach_system_v1
coach_system_v2
nutrition_coach_v1
workout_coach_v1
Every AI request should be traceable to:
model
prompt_version
configuration_version
knowledge_version
13.10 AI Output Validation
Model output must pass through validation before being returned.
Validation may include:
Schema validation.
Maximum length.
Forbidden content detection.
Unsupported claims detection where feasible.
JSON/schema validation for structured outputs.
Safety classification.
Invalid output must not be blindly returned.
13.11 Streaming
If streaming is implemented:
Request
 ↓
Authorization
 ↓
AI generation
 ↓
Stream chunks
 ↓
Client rendering
The system must handle:
disconnect
timeout
provider failure
partial response
cancellation
Partial responses must not be persisted as complete assistant messages.
13.12 Conversation Storage
Conversation records should contain:
conversation_id
user_id
message_id
role
content
model
prompt_version
created_at
metadata
Sensitive content should have appropriate retention controls.
13.13 Personalization Engine
Personalization must be deterministic where possible.
Inputs may include:
Goal
Experience
Activity level
Training frequency
Available equipment
Workout history
Performance
Weight trends
Nutrition preferences
User behavior
The system should produce controlled recommendation candidates rather than allowing the LLM to arbitrarily invent a complete fitness program.
13.14 Recommendation Pipeline
User State
 ↓
Eligibility Rules
 ↓
Candidate Generation
 ↓
Constraint Filtering
 ↓
Ranking
 ↓
Optional AI Explanation
 ↓
Recommendation
Business rules take precedence over AI-generated suggestions.
13.15 AI Cost Controls
The backend must track:
requests
tokens
latency
model
provider
estimated cost
errors
Implement:
Per-user rate limits.
Request size limits.
Conversation context limits.
Daily/monthly quotas where required.
Model fallback.
Caching where safe.
13.16 AI Failure Behavior
If AI is unavailable:
AI unavailable
 ↓
Return graceful fallback
 ↓
Do not expose provider errors
 ↓
Log technical error internally
The mobile application must never display raw provider/API/database errors.
14 — W8 Notifications, Events & Communication
14.1 Purpose
W8's communication architecture provides:
Push notifications.
In-app notifications.
Scheduled notifications.
Transactional notifications.
Product events.
System communication.
14.2 Event-Driven Architecture
Business events should be emitted from backend domain operations.
Example:
WorkoutCompleted
SubscriptionActivated
SubscriptionExpiring
GoalReached
PlanAssigned
NotificationPreferenceChanged
Events should be processed asynchronously where appropriate.
14.3 Event Structure
Every event should contain:
event_id
event_type
aggregate_type
aggregate_id
occurred_at
producer
schema_version
payload
Event IDs must support idempotency.
14.4 Event Processing
Domain Event
 ↓
Event Bus / Queue
 ↓
Consumer
 ↓
Notification Policy
 ↓
Preference Check
 ↓
Template
 ↓
Provider
 ↓
Delivery Result
14.5 Notification Types
TRANSACTIONAL
REMINDER
ENGAGEMENT
SYSTEM
SECURITY
MARKETING
Marketing notifications require explicit user consent where legally/product-wise required.
14.6 Notification Preferences
Users must be able to configure notification categories.
Example:
Workout reminders
Nutrition reminders
Progress updates
AI insights
Security alerts
Marketing
Critical security notifications may not be disableable.
14.7 Device Tokens
A user may have multiple devices.
Model:
User
 └── Device
      ├── platform
      ├── push_token
      ├── app_version
      ├── locale
      ├── timezone
      └── status
Tokens must be treated as sensitive infrastructure identifiers.
14.8 Push Providers
Push delivery must be abstracted behind a provider interface.
Example:
NotificationProvider
 ├── send()
 ├── validate()
 └── handleFailure()
This prevents business logic from depending directly on a specific provider.
14.9 Notification Templates
Templates must support:
Localization.
Variables.
Versioning.
Preview.
Validation.
Example:
Title:
"Time for your workout, {{first_name}}"

Body:
"Your {{workout_name}} session is ready."
Variables must be validated before sending.
14.10 Notification Scheduling
Scheduled notifications require:
scheduled_at
timezone
audience
template
status
Timezone must be user-aware.
Never assume server timezone equals user timezone.
14.11 Delivery States
QUEUED
PROCESSING
SENT
DELIVERED
FAILED
CANCELLED
EXPIRED
Provider capabilities determine whether DELIVERED can be reliably confirmed.
14.12 Retry Policy
Transient failures may be retried.
Permanent failures must not be retried indefinitely.
Use:
Exponential backoff
Maximum retry count
Dead-letter handling
Idempotency
14.13 Notification Security
Notifications must not expose sensitive information in lock-screen text.
Avoid:
"Your account password was changed to ..."
Prefer:
"Your W8 account security settings were updated."
14.14 Communication Logging
Store enough information to debug delivery:
notification_id
event_id
user_id
device_id
template_id
provider
status
failure_reason
created_at
sent_at
Do not unnecessarily store sensitive payload data.
15 — W8 Authentication, Identity & Account Lifecycle
15.1 Purpose
Authentication provides secure identity management across:
Mobile
Admin
Backend
AI
Payments
Notifications
Authentication and authorization are separate concerns.
Authentication = Who are you?
Authorization = What are you allowed to do?
15.2 Account Lifecycle
The lifecycle is:
REGISTERED
 ↓
EMAIL/IDENTITY_VERIFIED
 ↓
ONBOARDING
 ↓
ACTIVE
 ↓
SUSPENDED
 ↓
DELETION_REQUESTED
 ↓
DELETED
Some providers may allow different verification sequences.
15.3 Registration
Supported methods may include:
Email + Password
Google
Apple
The actual enabled providers must be controlled by configuration.
Registration must validate:
Email format.
Password policy.
Duplicate account rules.
Verification requirements.
Rate limits.
Abuse prevention.
15.4 Password Requirements
Passwords must:
Never be stored in plaintext.
Never be logged.
Never be returned by API.
Be hashed using a modern password hashing algorithm.
Be protected against brute force.
Preferred algorithms include:
Argon2id
or an approved equivalent.
15.5 Session Architecture
Authentication should use short-lived access tokens and secure refresh mechanisms.
Example:
Access Token
Short lifetime
        +
Refresh Token
Longer lifetime
Refresh tokens must be:
Securely stored.
Rotated.
Revocable.
Associated with a session/device.
Invalidated on suspicious activity where appropriate.
15.6 Session Model
Each authenticated session should have:
session_id
user_id
device_id
created_at
last_seen_at
expires_at
revoked_at
ip_metadata
user_agent_metadata
Sensitive raw credentials should not be stored.
15.7 Token Rotation
Refresh token rotation must detect reuse.
Example:
Refresh Token A
      ↓
Refresh
      ↓
Token B issued
      ↓
Token A invalidated
If revoked Token A is reused, the system should treat the session/token family as potentially compromised.
15.8 Email Verification
Unverified accounts should have limited access according to product policy.
Verification tokens must:
Expire.
Be single-use.
Be securely generated.
Be rate-limited.
Never appear in logs.
15.9 Password Reset
Flow:
Forgot Password
 ↓
Request reset
 ↓
Generic response
 ↓
Secure token/email
 ↓
Reset password
 ↓
Invalidate relevant sessions
The request response must not reveal whether an email exists.
Bad:
Email does not exist.
Good:
If an account exists, instructions will be sent.
15.10 Account Enumeration Protection
Authentication endpoints must avoid leaking:
Account existence.
Internal identifiers.
Provider information.
Rate limiting and abuse detection are required.
15.11 Social Login
Social authentication must use official OAuth/OIDC flows.
The backend must validate provider-issued identity tokens server-side.
Never trust:
user_id
email
provider claims
sent directly from the mobile client without verification.
15.12 Admin Authentication
Admin authentication must be isolated from normal user authentication where practical.
Admin users should require stronger security controls.
Recommended:
MFA
Shorter sessions
Stronger authorization
Audit logging
Risk-based controls
15.13 RBAC
Authorization must be server-side.
Example:
USER
ADMIN
SUPER_ADMIN
CONTENT_MANAGER
SUPPORT_AGENT
ANALYST
AI_MANAGER
Roles map to permissions.
Example:
CONTENT_MANAGER
 ├── exercises.read
 ├── exercises.create
 ├── exercises.update
 ├── exercises.submit_review
 ├── workouts.read
 └── workouts.create
No client-side role check can replace backend authorization.
15.14 Resource Ownership
Users may access only resources they are authorized to access.
Every backend operation must validate ownership or explicit permission.
Example:
GET /users/{id}/workouts
must verify:
authenticated_user.id == requested_user.id
unless the caller has an appropriate privileged permission.
15.15 Account Suspension
When an account becomes suspended:
New access tokens must be rejected.
Active sessions should be revoked.
Refresh tokens should be invalidated.
Push communication should follow suspension policy.
User data must remain according to retention requirements.
15.16 Account Deletion
Deletion should support a controlled lifecycle:
Deletion Requested
 ↓
Confirmation
 ↓
Grace Period if applicable
 ↓
Data Cleanup / Anonymization
 ↓
Account Deleted
Deletion must distinguish:
Personally identifiable data
Transactional records
Audit records
Legal retention data
Analytics aggregates
Not every record can necessarily be physically deleted immediately.
15.17 Data Export
Where supported, users should be able to request an export of their personal data.
Export processing should be asynchronous:
Request
 ↓
Authorization
 ↓
Export Job
 ↓
Generate
 ↓
Secure Temporary Storage
 ↓
User Download
 ↓
Expiration
15.18 Login Security
Implement:
Rate limiting.
Brute-force protection.
Suspicious-login detection where appropriate.
Session revocation.
Secure error responses.
Device/session management.
Do not permanently lock users based solely on simplistic failed-login counters without a recovery mechanism.
15.19 Authentication Logging
Security logs may record:
login_success
login_failure
logout
password_reset_requested
password_reset_completed
email_verified
session_created
session_revoked
refresh_token_reuse_detected
admin_login
MFA_event
Logs must never contain:
password
access_token
refresh_token
OTP
raw authentication secrets
15.20 Identity Linking
If a user links multiple authentication providers:
User
 ├── Email Identity
 ├── Google Identity
 └── Apple Identity
All identities must map to one internal W8 user account where verified and allowed.
Automatic account merging based only on matching email addresses must be handled carefully and must not create account-takeover vulnerabilities.
15.21 Authentication API Requirements
Authentication endpoints must enforce:
HTTPS.
Validation.
Rate limiting.
CSRF protection where cookie-based authentication is used.
Secure token handling.
Consistent error format.
Audit/security logging.
Idempotency where applicable.
15.22 Authentication Error Contract
The API must not expose internal authentication implementation details.
Use stable error codes.
Example:
{
  "code": "INVALID_CREDENTIALS",
  "message": "The email or password is incorrect."
}
Avoid:
PostgreSQL error
JWT exception
OAuth provider exception
stack trace
15.23 Identity Security Invariants
The following rules are mandatory:
No plaintext passwords.
No authentication secrets in logs.
No client-side-only authorization.
No direct database access from clients.
Access tokens must expire.
Refresh tokens must be revocable.
Refresh-token reuse must be detectable.
Admin privileges must be server-enforced.
Suspended accounts cannot continue normal authenticated access.
Password-reset requests must not reveal account existence.
OAuth/OIDC identity assertions must be verified server-side.
Every privileged authentication/security action must be auditable.
15.24 Account Lifecycle Invariant
The backend must be the authoritative source of account state.
The mobile application's local state may cache identity information, but it must never be considered authoritative for:
account status
subscription
permissions
entitlements
session validity
Section 11–15 Integration Requirements
These five sections must operate as one system:
Authentication
      │
      ▼
Authorization / RBAC
      │
      ▼
Admin / Content Management
      │
      ▼
Published W8 Content
      │
      ├──────────────┐
      ▼              ▼
Workout Engine   Nutrition Engine
      │              │
      └──────┬───────┘
             ▼
       Personalization
             │
             ▼
          AI Coach
             │
             ▼
       Domain Events
             │
             ▼
       Notification System
             │
             ▼
          User Device
Mandatory architectural rule
AI, Notifications, Admin, Mobile, Payments, and Content Management must never bypass the backend domain/security layer.
The backend remains the authoritative enforcement point for:
Identity.
Permissions.
User state.
Content state.
Workout rules.
Nutrition rules.
AI access.
Entitlements.
Notifications.
Auditability.
Cross-Section Non-Negotiable Rules
The implementation AI must treat the following as hard constraints:
1. No secrets in Flutter.
2. No provider API keys in the mobile application.
3. No direct database access from Mobile/Admin.
4. No authorization enforced only in UI.
5. No hardcoded production content in Flutter.
6. No hardcoded English UI strings.
7. No hardcoded Arabic UI strings.
8. No AI-generated content bypassing W8 safety/business rules.
9. No unpublished CMS content exposed to normal users.
10. No destructive Admin operation without authorization + confirmation + audit.
11. No authentication secret in logs.
12. No unbounded Admin queries.
13. No unlimited AI requests.
14. No notification sending without preference/policy evaluation.
15. No mutation of historical published content without versioning.
16. No silent failure of asynchronous operations.
17. Every production-facing feature must have loading/error/empty/offline behavior.
18. Every privileged operation must be auditable.
19. Every external provider must be behind an abstraction layer.
20. Backend validation is mandatory even when Flutter/Admin already validates the same data.
نهاية Sections 11–15.
