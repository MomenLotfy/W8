Section 21 — Privacy, Compliance & Data Governance
21.1 Purpose
W8 must treat user data as a controlled asset. Privacy, security, retention, access, export, deletion, and administrative access must be explicitly designed rather than added after implementation.
This section defines how W8 collects, stores, processes, exposes, retains, exports, and deletes user data.
Implementation principle: collect the minimum data required to provide a feature. Do not collect data simply because it may be useful later.
21.2 Data Classification
Every W8 data field must have a classification.
Classification
Examples
Default handling
Public
Exercise names, public articles, public plans
May be publicly readable
Internal
System configuration, internal analytics
Authenticated/internal access
Confidential
Email, subscription status, user preferences
Restricted
Sensitive
Weight, height, nutrition information, workout history
Strict access control
Highly Sensitive
Health integrations, health-related data, identity/security credentials
Strongest controls
The exact classification of a field must be documented in the domain model.
21.3 Data Minimization
W8 MUST NOT store:
unnecessary identity information
raw passwords
payment card numbers
unnecessary health data
unnecessary device information
unnecessary location history
unnecessary biometric information
raw authentication secrets
third-party access tokens in plaintext
If a feature can operate without storing a field, the field should not be stored.
21.4 User Data Ownership
A user's personal data must always be associated with a stable internal user_id.
The system must not use email as the primary relational identifier.
Recommended:
user.id
    ↓
profile.user_id
workout.user_id
nutrition.user_id
progress.user_id
notification.user_id
subscription.user_id
ai_conversation.user_id
21.5 Data Access Rules
Mobile user
A user may access only their own private data.
Authenticated User
        ↓
user_id from verified identity
        ↓
authorization check
        ↓
resource ownership check
        ↓
data
Never trust:
GET /users/{user_id}/progress
simply because the request is authenticated.
The backend must verify ownership.
21.6 Administrative Access
Admin access must be explicitly authorized.
Administrators must not automatically receive unrestricted access to all user data.
RBAC and permission checks defined in Section 07 apply.
Sensitive administrative actions must generate audit events.
Examples:
ADMIN_USER_VIEW
ADMIN_USER_SUSPEND
ADMIN_USER_EXPORT
ADMIN_USER_DELETE
ADMIN_CONTENT_PUBLISH
ADMIN_SUBSCRIPTION_MODIFY
21.7 AI Data Governance
W8 AI features must follow strict data boundaries.
AI systems MUST NOT automatically receive the entire user database.
The AI context should contain only information required for the current task.
Example:
User asks:
"Why did my workout performance decrease?"

AI context:
- recent workouts
- relevant exercises
- recent performance metrics
- relevant goals

Do NOT automatically include:
- email
- password
- payment information
- unrelated notifications
- administrative information
21.8 AI Training Policy
User conversations and personal data must not automatically become training data.
Unless explicitly designed and legally permitted, W8 must treat user conversations as operational data only.
21.9 Data Retention
Each data category must have a documented retention policy.
Example:
Data
Retention
Account
Until deletion
Workout history
Until deletion or configured retention
AI conversations
Configurable
Notifications
Limited retention
Audit logs
Long-term controlled retention
Application logs
Short-term
Security events
Longer retention
Backups
According to backup policy
Retention periods must be configurable where practical.
21.10 Account Deletion
W8 must support account deletion.
Deletion must be implemented as a controlled workflow:
Request deletion
      ↓
Authentication confirmation
      ↓
Deletion grace period
      ↓
Account disabled
      ↓
Personal data deletion/anonymization
      ↓
Related data cleanup
      ↓
Audit record retained
Security/audit records may need to remain after account deletion where legally or operationally necessary, but must not retain unnecessary personal data.
21.11 Data Export
Users should be able to request an export of their personal data.
Export should contain applicable:
profile information
preferences
workout history
nutrition history
progress data
AI conversation data where applicable
subscription information where appropriate
Export format:
JSON
CSV
The export must be generated asynchronously for large datasets.
21.12 Consent
Where consent is required, consent must be:
explicit
versioned
timestamped
associated with the user
revocable where applicable
Store:
consent_id
user_id
consent_type
policy_version
granted
created_at
revoked_at
Never overwrite historical consent records.
21.13 Third-Party Data
Every external integration must document:
Provider
Purpose
Data sent
Data received
Authorization method
Retention
Revocation mechanism
Failure behavior
Examples:
Apple Health
Google services
payment provider
push notification provider
AI provider
analytics provider
21.14 Secrets
Secrets must never be stored in:
Git
source code
frontend bundles
Docker images
logs
database records unless encrypted and specifically required
Use environment/secret management infrastructure.
21.15 Privacy by Design
Every new W8 feature must answer:
What data does it collect?
Why is the data required?
Where is it stored?
Who can access it?
How long is it retained?
Can the user delete it?
Is it sent to a third party?
Is it sent to an AI provider?
What happens if the user revokes permission?
A feature is not production-ready until these questions are answered.
Section 22 — Backup, Disaster Recovery & Business Continuity
22.1 Objective
W8 must be recoverable from:
database corruption
accidental deletion
infrastructure failure
deployment failure
cloud outage
security incident
application bug
regional outage
22.2 Recovery Objectives
Initial production targets:
Metric
Target
RPO
≤ 15 minutes
RTO
≤ 1 hour
Backup verification
Automated
Restore testing
Scheduled
These targets may be adjusted according to infrastructure cost and actual business requirements.
22.3 Backup Strategy
Primary database:
PostgreSQL
   ↓
Automated continuous backup
   ↓
Point-in-time recovery
Backups must be:
encrypted
access controlled
monitored
versioned
tested
22.4 Backup Types
Database
continuous WAL/PITR where supported
daily full backup
periodic long-term backup
Object storage
Versioning should be enabled for critical objects.
Configuration
Infrastructure configuration must exist in Git.
Secrets must be backed up through a secure secrets-management mechanism, not Git.
22.5 Backup Isolation
Backups must not depend entirely on the same infrastructure they protect.
Recommended:
Production
    ↓
Primary backup
    ↓
Separate backup location
For critical production systems, maintain an isolated backup copy.
22.6 Restore Testing
A backup is not considered valid simply because it exists.
The system must periodically perform:
Backup
 ↓
Restore
 ↓
Integrity verification
 ↓
Application startup
 ↓
Smoke tests
Restore tests must be documented.
22.7 Disaster Recovery Procedure
Example:
Incident detected
      ↓
Incident classification
      ↓
Protect remaining infrastructure
      ↓
Determine recovery point
      ↓
Provision recovery environment
      ↓
Restore database
      ↓
Restore storage
      ↓
Deploy application
      ↓
Run migrations if required
      ↓
Run smoke tests
      ↓
Validate critical flows
      ↓
Restore traffic
      ↓
Monitor
22.8 Business Continuity
Critical W8 functionality:
Authentication
User profile
Workout
Nutrition
Content
Subscription validation
Notifications
Admin operations
AI functionality should be designed as degradable, not as a single point of failure.
If AI is unavailable:
Core W8 application continues working.
22.9 Disaster Scenarios
The recovery plan must explicitly test:
database failure
Redis failure
object storage failure
API deployment failure
corrupted migration
accidental content deletion
compromised credentials
AI provider outage
payment provider outage
notification provider outage
complete infrastructure outage
Section 23 — Release Management & Versioning
23.1 Versioning Strategy
W8 consists of independently versioned components:
Mobile
API
Admin
Database schema
Infrastructure
Content
AI prompts/configuration
Application versioning should follow:
MAJOR.MINOR.PATCH
Example:
1.4.2
23.2 Version Meaning
MAJOR
Breaking product/API changes.
MINOR
Backward-compatible feature additions.
PATCH
Bug/security fixes.
23.3 API Versioning
API routes should use explicit versioning.
Example:
/api/v1/auth
/api/v1/workouts
/api/v1/nutrition
/api/v1/ai
Breaking changes require a new API version.
23.4 Database Migrations
Every schema change must have a migration.
Never modify production schema manually.
Workflow:
Code change
 ↓
Migration
 ↓
Tests
 ↓
Staging
 ↓
Production
Migration must be:
deterministic
reversible where practical
reviewed
tested against realistic data
23.5 Mobile Releases
Mobile releases must include:
version
build number
release notes
supported API version
migration considerations
known issues
23.6 Feature Flags
Risky features should be released behind feature flags.
Example:
AI_COACH_ENABLED
NEW_WORKOUT_ENGINE
PREMIUM_V2
APPLE_HEALTH_SYNC
Feature flags allow:
Deploy ≠ Release
This is important for safe production rollout.
23.7 Rollout Strategy
For high-risk releases:
Internal
 ↓
QA
 ↓
Staging
 ↓
Small production percentage
 ↓
Monitor
 ↓
Increase rollout
 ↓
100%
23.8 Rollback
Every production deployment must have a rollback strategy.
Application rollback:
Previous known-good version
Database rollback requires special handling.
Never blindly reverse migrations if data loss is possible.
Prefer forward-compatible database migrations.
23.9 Content Versioning
Content must also be versioned.
Examples:
Exercise v3
Workout Plan v5
Nutrition Plan v2
AI Prompt v4
Published content should not be silently modified in ways that alter historical user records.
Section 24 — Accessibility, Localization & Internationalization
24.1 Accessibility Objective
W8 must be usable by users with different:
visual abilities
motor abilities
hearing abilities
cognitive needs
Accessibility is a product requirement, not an optional enhancement.
24.2 Flutter Accessibility
All interactive controls must use semantic Flutter widgets.
Preferred:
ElevatedButton
TextButton
IconButton
Switch
Checkbox
Radio
ChoiceChip
NavigationBar
TextField
Avoid using:
GestureDetector
as a replacement for a native semantic control.
24.3 Tap Targets
Minimum:
44 × 44 logical pixels
This applies to:
buttons
icons
navigation
table actions
controls
close buttons
24.4 Screen Reader Support
Every important element must have meaningful semantics.
Examples:
"Day score: 82 percent"
"Start workout"
"Enable workout reminders"
"Premium feature locked"
Decorative elements should not unnecessarily enter the accessibility tree.
24.5 Charts
Charts must expose textual information.
Instead of:
████████
only visually,
provide semantics such as:
Monday: 8,200 steps
Tuesday: 9,100 steps
Wednesday: 7,800 steps
24.6 Dynamic Text Scaling
W8 must support system text scaling without:
clipping
overlapping
disappearing buttons
broken navigation
Layouts must be tested with increased text scale.
24.7 Localization
Initial locales:
en
ar
Arabic is first-class.
Do not implement Arabic as an afterthought.
24.8 RTL
Arabic must use:
Directionality
TextDirection.rtl
Avoid manual left/right positioning.
Use:
start
end
conceptually rather than:
left
right
where appropriate.
24.9 Localization Architecture
Use Flutter localization tooling:
flutter_localizations
intl
ARB files
generated AppLocalizations
Recommended:
l10n/
├── app_en.arb
└── app_ar.arb
Every user-visible string must originate from localization resources.
24.10 Numbers & Dates
Number formatting must be centralized.
Examples:
weight
height
calories
distance
duration
dates
times
percentages
Do not format numbers independently in every screen.
24.11 Arabic Content
Arabic translations must be human-reviewed.
AI-generated translation may assist development but must not automatically become production copy for important health, nutrition, payment, security, or legal content without review.
Section 25 — UX/UI & Design System Implementation
25.1 Objective
The Flutter application must reproduce the approved W8 prototype's visual language while converting prototype-only behavior into production-grade native UX.
The prototype is the visual reference, not the technical implementation.
The audit report defines the known prototype gaps.
25.2 Source of Truth Hierarchy
When implementing UI, use this priority:
1. W8 Master Technical Specification
2. Approved product/business rules
3. Approved prototype
4. Design tokens
5. Implementation decisions
If the prototype conflicts with the Master Specification, the Master Specification wins.
25.3 Design Tokens
All visual primitives must come from centralized tokens.
Required:
Colors
Typography
Spacing
Radius
Elevation
Motion
Breakpoints
Component dimensions
Widgets must not contain arbitrary colors such as:
Color(0xFFFF5C00)
Instead:
Theme → NurColors → accent
25.4 Color System
W8 retains the approved orange accent:
Accent: #FF5C00
However, raw colors must only exist inside the theme/token layer.
Required semantic colors:
background
surface
surface2
surface3
border
borderStrong
textPrimary
textSecondary
textTertiary
accent
accentStrong
accentTint
success
warning
danger
info
25.5 Typography
The prototype identified an important font issue:
Somar Sans was referenced but not actually loaded.
Before production implementation, this must be resolved.
Two valid approaches:
Option A
License and bundle Somar Sans.
Option B
Officially standardize on Inter + IBM Plex Sans Arabic + IBM Plex Mono.
The final decision must be recorded before UI implementation.
No accidental fallback fonts are allowed.
25.6 Spacing
Use the 4px spacing scale:
4
8
12
16
20
24
32
40
48
64
Widgets should consume semantic spacing tokens.
25.7 Radius
sm   8
md   12
lg   16
xl   24
pill 999
25.8 Buttons
W8 button family:
Primary
Secondary
Ghost
Danger
Icon
Small
Full width
Loading
Disabled
Every button must define:
enabled
disabled
loading
pressed
focused
accessibility label
25.9 Cards
Two primary variants:
NurCard.elevated
NurCard.flat
No screen should create a new card style without a documented design-system reason.
25.10 Inputs
Real Flutter input controls must be used.
Required states:
Default
Focused
Filled
Disabled
Error
Success
Loading
Validation must be visible and accessible.
25.11 Bottom Sheets
All W8 sheets should share one scaffold.
Examples:
Settings
Profile
Premium
Day Score
Meal Replace
They must support:
safe-area handling
keyboard handling
dismissal
accessibility focus
Android back
iOS swipe dismissal where appropriate
25.12 Confirmation Dialogs
Reusable confirmation dialog:
NurConfirmDialog
Required for destructive actions:
Delete
Suspend
Cancel
Archive
Sign out where appropriate
Account deletion
Content deletion
The dialog must clearly explain:
Action
Consequence
Cancel
Confirm
25.13 Navigation
Mobile bottom navigation:
Dashboard
Workout
AI Coach
Nutrition
History
Notifications remains accessible through Settings unless the product specification changes this decision.
The implementation must preserve navigation state appropriately.
25.14 Workout UX
Workout navigation:
Plan
 ↓
Library
 ↓
Exercise Detail

Plan
 ↓
Logging
 ↓
Summary
 ↓
Dashboard
Exit workout:
Logging
 ↓
Confirmation
 ↓
Save / Discard
This behavior must be represented by actual navigation/state management, not UI-only transitions.
25.15 Loading / Empty / Error / Offline
Every major feature must define:
Loading
Empty
Error
Offline
Success
The reusable:
NurStateBlock
should be the visual implementation.
Example:
Workout
├── Loading
├── Data
├── Empty
├── Error
└── Offline
The same pattern applies to:
Dashboard
Nutrition
AI Coach
History
Notifications
Admin
25.16 Premium UI
Premium content must use a consistent locked-state pattern.
Required:
blurred/obscured content
lock indicator
explanation
upgrade CTA
The UI must never be considered authorization.
Backend entitlement checks remain authoritative.
25.17 Motion
Approved motion:
120ms
220ms
Motion should communicate:
navigation
state transition
success
loading
modal presentation
Avoid excessive animation.
Users must still be able to understand the interface when animations are reduced.
25.18 Responsive Layout
Mobile UI must adapt to:
small phone
normal phone
large phone
tablet
Admin must additionally support:
desktop
large desktop
Use:
MediaQuery
LayoutBuilder
responsive breakpoints
Never reproduce the prototype's fixed:
375 × 812
device frame.
25.19 AI Coach UX
The AI Coach must use a real:
TextField
and support:
typing
send
loading
streaming
error
retry
empty conversation
quick replies
keyboard avoidance
message history
AI responses must visually distinguish:
User
AI
System/error
25.20 Notifications UX
Notifications must support:
Preferences
History
Permission state
Enabled
Disabled
Denied
System settings required
Loading
Saving
Saved
Error
Preference changes must be persisted through the backend where applicable.
25.21 Admin UI
Admin UI must not be treated as a second-class prototype.
It requires:
Responsive sidebar
Data tables
Pagination
Search
Filters
Loading
Empty
Error
Success
Confirmation
Role restrictions
Audit visibility
25.22 UI Component Architecture
Recommended structure:
presentation/
├── theme/
│   ├── nur_colors.dart
│   ├── nur_spacing.dart
│   ├── nur_radius.dart
│   ├── nur_elevation.dart
│   ├── nur_motion.dart
│   └── nur_typography.dart
│
└── widgets/
    ├── buttons/
    ├── inputs/
    ├── cards/
    ├── navigation/
    ├── progress/
    ├── charts/
    ├── sheets/
    ├── dialogs/
    ├── states/
    └── accessibility/
25.23 Design System Rules
Rule 1
No raw colors in feature widgets.
Rule 2
No arbitrary spacing.
Rule 3
No duplicated component implementations.
Rule 4
No screen-specific button styles unless documented.
Rule 5
No hardcoded user-facing strings.
Rule 6
No direct API calls from UI widgets.
Rule 7
No business logic inside presentation widgets.
Rule 8
No UI component may bypass accessibility requirements.
25.24 Design-System Testing
Every reusable component must be tested for:
light theme
dark theme
English
Arabic
RTL
accessibility
disabled state
loading state
error state where applicable
small screens
large text
keyboard interaction where applicable
Cross-Section Engineering Requirement
Sections 21–25 are not isolated documentation. They establish mandatory implementation constraints for the rest of W8.
The AI implementing W8 must therefore treat the following as non-negotiable:
Privacy by design
        ↓
Least data collection
        ↓
Strict authorization
        ↓
Encrypted/protected storage
        ↓
Auditable administration
        ↓
Recoverable infrastructure
        ↓
Versioned releases
        ↓
Accessible UI
        ↓
Full EN/AR localization
        ↓
Centralized design system
        ↓
Reusable components
        ↓
Tested production behavior
Final implementation rule
The prototype defines how W8 should look.
The Master Specification defines what W8 must do.
The security architecture defines what W8 is allowed to do.
The design system defines how W8 should be implemented visually.
AGENTS.md defines how the AI must perform the implementation.
وبكده Section 21 → 25 جاهزين للإضافة مباشرة إلى W8_MASTER_TECHNICAL_SPECIFICATION.md بنفس أسلوب الوثيقة، ومش محتاجين تحط تقرير الـaudit داخل الـMaster نفسه مرة تانية. تقرير الـaudit يفضل كـ reference document داخل docs/design/، بينما الـMaster يحتوي القرارات والمواصفات النهائية فقط.
