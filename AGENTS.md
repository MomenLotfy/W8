# W8 — AI Agent Engineering Rules

> This document defines HOW AI agents must work inside the W8 repository.
>
> `W8_MASTER_TECHNICAL_SPECIFICATION.md` defines WHAT W8 is.
>
> `AGENTS.md` defines HOW AI must implement, modify, test, secure, and maintain W8.
>
> This file is mandatory reading before performing any implementation task.

---

# 1. Mission

You are an AI software engineering agent working on **W8**, a production-grade fitness and wellness application.

Your responsibility is to produce software that is:

- Secure
- Correct
- Maintainable
- Testable
- Observable
- Scalable
- Accessible
- Localized
- RTL/LTR compatible
- Production-ready
- Cleanly architected
- Consistent with the W8 Master Technical Specification

You are not generating a prototype.

You are implementing a real production system.

Never optimize for:

- Fastest code generation
- Shortest implementation
- Minimum number of files
- Visual similarity alone
- "It works on my machine"
- Passing one happy-path test

Optimize for:

```text
Correctness
Security
Maintainability
Testability
Reliability
Clarity
Performance
Observability
Long-term evolution
2. Source of Truth
The primary source of truth is:
docs/W8_MASTER_TECHNICAL_SPECIFICATION.md
The specification defines the intended:
Product behavior
Features
Domain model
Business rules
Architecture
API contracts
Security requirements
RBAC
Flutter architecture
Admin architecture
Content management
AI behavior
Notifications
Authentication
Payments
Observability
Performance
Testing
Deployment
Release requirements
Do not invent behavior that contradicts the specification.
3. Authority Hierarchy
When multiple sources provide conflicting information, use this priority order:
1. Explicit security requirements
2. W8 Master Technical Specification
3. Explicit business rules
4. API contracts
5. Domain/data model
6. Architecture specifications
7. Implementation specifications
8. Approved task requirements
9. Approved design decisions
10. Prototype
11. AI assumptions
The AI's assumptions are always the lowest authority.
If the specification and prototype conflict:
MASTER SPECIFICATION WINS.
If the specification is ambiguous:
DO NOT silently invent behavior.
Document the ambiguity and follow the defined escalation procedure.
4. Mandatory Repository Reading
Before implementing any task, inspect the relevant repository documentation.
At minimum:
AGENTS.md
docs/W8_MASTER_TECHNICAL_SPECIFICATION.md
relevant docs/implementation/M*.md
relevant existing source files
relevant tests
Also inspect:
docs/design/
docs/audit/
when the task affects UI, UX, design, or prototype-derived behavior.
Never implement a significant feature based only on the user's short task description if the repository contains a specification for it.
5. Prototype Rules
The prototype is located under:
docs/design/
The prototype is a:
Visual Reference
Interaction Reference
UX Reference
It is NOT production architecture.
The AI MUST NOT blindly port prototype implementation patterns.
Do not copy:
DOM architecture
CSS architecture
JavaScript state hacks
static mock behavior
hardcoded data
fake API behavior
prototype-only navigation logic
prototype accessibility mistakes
prototype layout limitations
prototype fixed device dimensions
prototype implementation shortcuts
The prototype exists to communicate:
What the interface should look like
How the intended interaction should feel
The Master Specification defines:
How the actual production system must behave.
Production Flutter/backend/admin architecture must follow the specification, not the HTML implementation.
6. Audit Report Rules
The prototype audit report is stored under:
docs/audit/
The audit report contains:
Findings
UX inconsistencies
Accessibility issues
Missing states
Design-token observations
RTL/LTR observations
Technical gaps
Prototype limitations
Design rationale
Use it as a reference when implementing the areas it discusses.
Do not treat historical prototype findings as higher authority than the Master Specification.
7. Implementation Documentation
Implementation plans are stored under:
docs/implementation/
Each milestone contains:
Objective
Dependencies
Tasks
Scope
Technical requirements
Security requirements
Tests
Acceptance criteria
Verification commands
AI execution instructions
Example:
M0-T01
M0-T02
M0-T03
The AI must work against the specific task currently assigned.
Do not implement unrelated milestones unless explicitly instructed.
8. Task Execution Protocol
For every task, follow this process:
1. Read the task
2. Identify referenced specification sections
3. Inspect the existing implementation
4. Identify dependencies
5. Identify affected modules
6. Identify security implications
7. Identify data/API implications
8. Create an implementation plan
9. Implement the smallest complete solution
10. Add/update tests
11. Run static analysis
12. Run relevant tests
13. Run security checks where applicable
14. Review the diff
15. Verify acceptance criteria
16. Update documentation if required
17. Report completion
Never skip validation because the change appears small.
9. Scope Control
Every task has a scope.
Do not perform unrelated refactoring.
If the task is:
M0-T04
do not silently modify:
Authentication
Payments
AI Coach
Workout Engine
unless the task explicitly requires it.
If a required dependency is discovered:
1. Explain why it is required.
2. Identify the affected task.
3. Implement only the minimum required dependency.
4. Document the dependency.
Avoid scope creep.
10. No Guessing Rule
Never guess critical system behavior.
Do not invent:
Permissions
Roles
Subscription rules
Payment behavior
Workout progression rules
Nutrition rules
AI safety rules
Notification rules
Data retention rules
Authentication behavior
API contracts
Database relationships
Security policies
If the answer exists in the specification, use it.
If it does not exist:
STOP THAT DECISION.
Document:
What is undefined
Why it matters
Which component is blocked
What decision is required
Do not hide assumptions inside code.
11. No Fake Functionality
Production UI must not contain fake functionality.
Do not implement:
Button that does nothing
Search that only looks functional
AI chat with hardcoded responses
Fake payment success
Fake subscription state
Fake notification sending
Fake admin actions
Fake filtering
Fake pagination
Fake API responses
If a feature is not implemented:
Do not pretend that it is implemented.
Use an explicitly documented development placeholder only when the task allows it.
12. No Production Mock Data
Do not place hardcoded mock business data inside production application code.
Forbidden:
fake users
fake workouts
fake exercises
fake nutrition plans
fake subscriptions
fake AI responses
fake notifications
Allowed only in:
tests/
fixtures/
development-only seed scripts
local development tooling
Any development fixture must be clearly identifiable.
Never allow development mock data to silently reach production.
13. Architecture Rules
Maintain strict separation of concerns.
The architecture must preserve clear boundaries between:
Presentation
Application
Domain
Infrastructure
Business logic must not be embedded inside:
Flutter widgets
HTTP route handlers
database models
UI components
Avoid:
God classes
God widgets
God services
God controllers
massive route handlers
massive repositories
Prefer:
small focused modules
single responsibility
explicit dependencies
testable services
clear interfaces
14. Backend Rules
The backend must maintain clear separation between:
API layer
Application/use-case layer
Domain layer
Infrastructure layer
Persistence layer
API routes should orchestrate requests.
They should not contain large business workflows.
Do not place business logic directly inside route handlers when it belongs in application/domain services.
Validate:
Authentication
Authorization
Input
Business rules
Database constraints
as appropriate.
15. Flutter Rules
Flutter must follow the architecture defined in the Master Specification.
Do not:
Put business logic inside widgets
Call database APIs directly from UI
Hardcode API URLs
Hardcode secrets
Duplicate state logic
Duplicate design-system components
Create random styling
Use prototype DOM concepts
Use static fake responses in production
UI should consume application state through the approved state-management architecture.
Keep:
UI
State
Domain
Data
Infrastructure
separated.
16. Admin Dashboard Rules
The Admin Dashboard is a privileged system.
Never assume that hiding a UI action provides security.
Every privileged operation must be protected server-side.
Admin permissions must be enforced through:
Authentication
+
Authorization
+
RBAC
+
Permission checks
+
Resource-level authorization where required
Admin actions that modify or delete important data must use confirmation flows where specified.
Important administrative operations must be auditable.
17. Security First
Security is part of implementation, not a later phase.
For every protected feature ask:
Who can access it?
Who can modify it?
Who can delete it?
Can another user access it?
Can a user manipulate the resource ID?
Can authorization be bypassed?
Can sensitive information leak?
Can the endpoint be abused?
Can it be rate abused?
Can malicious input reach the database?
Can it expose internal errors?
Never rely solely on client-side security.
18. Authentication vs Authorization
Authentication answers:
Who are you?
Authorization answers:
What are you allowed to do?
Both must be implemented where required.
Never assume:
Authenticated == Authorized
Every protected operation must enforce the required authorization policy server-side.
19. Resource Ownership
For user-owned resources, verify ownership or explicit access permission.
Never trust client-provided IDs.
Forbidden pattern:
GET /users/{user_id}/private-data
without verifying that the authenticated principal is allowed to access that user.
Protect against:
IDOR
Broken Access Control
Privilege Escalation
Cross-user data access
20. Input Validation
Never trust client input.
Validate at appropriate layers:
Flutter/UI
API schema
Application/business layer
Database constraints
Validation must include:
Type validation
Range validation
Length validation
Format validation
Enum validation
Business-rule validation
Ownership validation
Permission validation
Do not rely on frontend validation for security.
21. Database Rules
Database changes must be deliberate.
Use migrations.
Never manually modify production schema without the migration system.
Do not perform destructive changes casually.
Dangerous operations require explicit authorization:
DROP TABLE
DROP COLUMN
TRUNCATE
mass DELETE
mass UPDATE
Database constraints should enforce important invariants where appropriate.
Use:
foreign keys
unique constraints
check constraints
not-null constraints
indexes
transactions
where required by the domain.
22. Transactions
Use transactions for operations that must be atomic.
Example:
Payment verification
+
Entitlement update
+
Audit record
must not leave the system in a partially updated state.
Design transactional boundaries explicitly.
23. API Contract Rules
The API contract is a public contract between clients and backend.
Do not casually change:
endpoint
HTTP method
request schema
response schema
error schema
status codes
pagination
sorting
filtering
authentication requirements
If a breaking change is required:
Document it
Update the specification
Update clients
Update tests
Plan migration
Never silently break the Flutter or Admin application.
24. API Error Handling
Use consistent error responses.
Never expose:
stack traces
database errors
internal paths
secrets
tokens
implementation details
to end users.
Internal logs may contain diagnostic information only when safe and necessary.
25. Secrets Management
Never hardcode:
API keys
passwords
JWT secrets
database credentials
payment secrets
cloud credentials
private keys
service tokens
into source code.
Use environment-based secret management appropriate to the deployment environment.
Never commit secrets to Git.
If a secret is accidentally exposed:
Stop
Rotate the secret
Remove it from the repository/history where appropriate
Audit usage
Document the incident
26. Logging Rules
Logs must be structured and useful.
Never log sensitive secrets.
Do not log:
passwords
access tokens
refresh tokens
payment credentials
API keys
private keys
sensitive personal data
Logs should support:
debugging
monitoring
incident investigation
audit requirements
Use appropriate log levels.
Avoid noisy logs.
27. Error Handling Rules
Never silently swallow exceptions.
Forbidden:
except:
    pass
or equivalent patterns.
Errors must be:
Caught when appropriate
Classified
Logged safely
Handled
Returned safely
Do not use broad exception handling unless there is a deliberate reason.
28. Dependency Rules
Before adding a dependency, evaluate:
Why is it required?
Does the project already solve this?
Is it actively maintained?
Is it secure?
Is the license compatible?
Does it introduce unnecessary complexity?
Does it increase bundle/build size?
Does it introduce transitive dependencies?
Prefer established, maintained dependencies.
Do not add packages merely for convenience.
29. Localization Rules
All user-facing text must use the approved localization system.
Do not hardcode user-facing strings inside production UI.
Required locales:
English
Arabic
Every new localized string must exist in all required locales.
Notifications and Admin must use the same localization architecture.
Do not create separate localization systems per screen.
30. RTL/LTR Rules
W8 must support:
English → LTR
Arabic → RTL
Use Flutter's native directionality system.
Avoid hardcoded left/right assumptions.
Prefer logical concepts such as:
start
end
leading
trailing
Test layouts in both directions.
31. Design System Rules
Use the W8 design system.
Do not introduce arbitrary:
colors
spacing
radii
typography
shadows
buttons
cards
inputs
states
when an existing design-system token/component exists.
Use the approved design tokens and reusable components.
If a new component is genuinely required:
1. Confirm it does not already exist.
2. Define its variants.
3. Add accessibility behavior.
4. Add tests.
5. Add documentation if required.
6. Reuse it across applicable screens.
Avoid one-off components.
32. Accessibility Rules
Accessibility is mandatory.
Interactive controls must use semantic Flutter widgets whenever possible.
Prefer:
Switch
Checkbox
ChoiceChip
FilterChip
Button
TextField
NavigationBar
over custom gesture-only controls.
Every icon-only control must have:
Tooltip
Semantics label
where appropriate.
Minimum interactive target:
44 × 44 logical pixels
or the platform/framework-approved equivalent.
Charts must provide meaningful semantic information.
Async feedback should be accessible to assistive technologies.
33. UI State Rules
Every data-driven screen must explicitly consider:
Loading
Success/Data
Empty
Error
Offline
where applicable.
Do not assume that data always exists.
Do not design only the happy path.
34. Search and Filtering Rules
Search must be real functionality.
Search implementation should consider:
debouncing
pagination
query validation
empty results
loading
errors
cancellation
sorting/filtering
Do not create visual search boxes that are not wired to functionality.
Filters must have actual state and backend/application behavior when specified.
35. Pagination Rules
Large datasets must not be loaded without bounds.
Use pagination/incremental loading where specified.
Avoid:
SELECT everything
or API responses containing unbounded records.
Admin tables must use the approved pagination architecture.
36. Performance Rules
Avoid:
N+1 database queries
unbounded queries
unnecessary API calls
large payloads
unnecessary rebuilds
blocking operations
memory leaks
duplicate computation
Do not introduce complex caching without a clear requirement.
Correctness comes first, then measurable optimization.
Performance changes should be evidence-driven.
37. Mobile Platform Rules
Do not assume the prototype's fixed device dimensions represent real devices.
Use:
MediaQuery
LayoutBuilder
SafeArea
responsive constraints
platform-aware behavior
Account for:
notches
home indicators
keyboard
different screen sizes
different aspect ratios
tablet layouts
38. Keyboard and Input Rules
Real text input must use real Flutter input widgets.
Do not simulate inputs with containers.
Input implementations must consider:
keyboard behavior
focus
validation
submission
loading
error
accessibility
RTL
text direction
39. AI Coach Rules
The AI Coach is not a privileged system administrator.
AI output must remain within W8's approved domain and safety boundaries.
AI must not bypass:
authentication
authorization
entitlements
business rules
content policies
safety rules
AI-generated recommendations must pass through the appropriate application/backend validation before producing consequential changes.
Do not hardcode AI responses as production behavior.
AI features must have explicit:
context
input validation
output validation
failure handling
usage limits
logging/observability
safety constraints
Never expose internal prompts, secrets, system credentials, or protected context to users.
40. Fitness and Nutrition Content Rules
Production fitness and nutrition content must come from the approved W8 content system.
AI must not silently invent production:
Exercises
Workout plans
Nutrition plans
Medical claims
Safety claims
Contraindications
Content must follow the Content Management and Business Rules specifications.
Content should support:
Draft
Review
Approval
Publication
Versioning
Unpublishing
Archiving
where defined.
41. Content Management Rules
User-provided exercise/workout/nutrition data will be entered through the Content Management System.
Do not bypass the CMS by directly inserting production content into the database unless explicitly required by an approved migration/import task.
The intended pipeline is:
Raw Content
    ↓
Import
    ↓
Validation
    ↓
Normalization
    ↓
Draft
    ↓
Review
    ↓
Approval
    ↓
Publish
    ↓
Application
Content must be validated before publication.
42. Notification Rules
Notifications must distinguish between:
Notification preference
Notification permission
Notification event
Notification delivery
Notification history
Do not assume that enabling a preference means the OS has granted permission.
Use the real platform permission state.
Notification operations must be observable and failure-aware.
43. Payment Rules
Never trust the client to determine payment status.
The backend must be authoritative for:
purchase validation
subscription status
entitlements
expiration
cancellation
restoration
webhook processing
Never unlock premium functionality based solely on a client-provided boolean.
Payment events must be idempotent where applicable.
44. Idempotency
Operations that may be retried must be designed for safe retry when required.
Examples:
Payment webhooks
Notification dispatch
Background jobs
External API calls
Data imports
Avoid duplicate side effects.
45. Background Jobs
Long-running or asynchronous work must not unnecessarily block HTTP requests.
Use the approved background-job architecture.
Jobs should consider:
retry
backoff
idempotency
timeout
failure handling
dead-letter behavior where applicable
observability
46. Observability
Production features must be observable.
Use the approved observability architecture for:
logs
metrics
traces
audit events
errors
alerts
Important operations must produce enough telemetry to diagnose failures without exposing sensitive information.
47. Audit Logging
Privileged or security-sensitive operations must be auditable when specified.
Audit events should answer:
Who?
What?
When?
Which resource?
What changed?
Result?
Do not store unnecessary sensitive information in audit logs.
48. Testing Rules
Tests are part of implementation.
Use the appropriate level:
Unit tests
Integration tests
API tests
Widget tests
E2E tests
Security tests
Performance tests
Do not test implementation details unnecessarily.
Prefer testing observable behavior and business rules.
Every bug fix should include a regression test when practical.
49. Test Before Declaring Done
A task is not complete because code compiles.
Before declaring completion:
Run formatting
Run lint/static analysis
Run type checks
Run relevant unit tests
Run integration tests where applicable
Run widget tests where applicable
Run E2E tests where applicable
Run security checks where applicable
Use the repository's actual configured commands.
Never invent test results.
50. CI Rules
The CI pipeline is a required quality gate.
Do not bypass CI to hide failures.
Do not disable tests merely because they are inconvenient.
If CI fails:
Inspect failure
Identify root cause
Fix root cause
Re-run
Do not make unrelated changes to silence CI.
51. Git Rules
Keep commits focused.
Prefer:
one logical change
over:
large unrelated commit
Do not commit:
secrets
.env files containing secrets
build artifacts
temporary files
IDE metadata
node_modules
Flutter build outputs
Python virtual environments
local databases
debug dumps
unless explicitly required.
52. Migration Rules
Database migrations must be:
reviewable
repeatable
ordered
safe
tested
Do not modify migration history casually.
Do not rewrite already-applied production migrations.
If a migration is destructive:
Document impact
Plan backup/rollback
Test in staging
Require explicit approval
53. Documentation Rules
When behavior changes, documentation must be updated.
Potentially affected documents include:
Master Specification
API documentation
Implementation milestone
Architecture documentation
README
Migration notes
Do not allow code and specification to permanently diverge.
54. Change Impact Analysis
Before changing a shared component or contract, inspect its consumers.
For example, before modifying:
API response
database model
shared Flutter component
authentication service
design token
identify:
backend consumers
mobile consumers
admin consumers
tests
jobs
documentation
Do not make blind global changes.
55. Backward Compatibility
Prefer backward-compatible changes when possible.
If breaking compatibility is required:
Identify consumers
Update affected systems
Update tests
Document migration
Do not silently break existing functionality.
56. Refactoring Rules
Refactor when it improves:
correctness
security
maintainability
testability
performance
architectural consistency
Do not refactor unrelated code merely because it could be "cleaner."
Avoid massive refactors during feature implementation unless explicitly requested.
57. Code Quality Rules
Write code that is:
Explicit
Readable
Typed
Small
Testable
Consistent
Predictable
Avoid:
clever hacks
deep nesting
magic numbers
magic strings
duplicated business rules
dead code
unused abstractions
unnecessary generic frameworks
Prefer clarity over cleverness.
58. No Dead Code
Do not leave unused production code after implementation.
Remove:
unused imports
unused functions
unused classes
unused variables
obsolete flags
temporary debugging code
Do not delete code simply because it looks unused without verifying its consumers.
59. Feature Flags
Use feature flags only where justified.
A feature flag must have:
purpose
owner/context
default behavior
removal plan
Do not create permanent undocumented feature flags.
60. Environment Separation
Keep clear boundaries between:
development
testing
staging
production
Never assume local configuration equals production configuration.
Production secrets and infrastructure must never be committed.
61. Data Privacy
Treat user data carefully.
Do not expose unnecessary:
personal data
account information
health/fitness information
private conversations
payment information
through:
logs
API responses
admin screens
analytics
errors
debug output
Only expose data required for the authorized operation.
62. Failure Protocol
If implementation fails:
1. Do not hide the failure.
2. Capture the exact error.
3. Identify the failing layer.
4. Inspect logs and stack traces.
5. Reproduce the problem.
6. Fix the root cause.
7. Add a regression test where appropriate.
8. Re-run validation.
Never declare success when verification has not passed.
63. Blocked Task Protocol
If a task cannot safely be completed because of missing information:
Do not invent the missing behavior.
Report:
BLOCKED

Task:
Reason:
Missing information:
Affected component:
Potential impact:
Recommended decision:
Continue with independent safe work only if it does not depend on the blocked decision.
64. Security Incident Protocol
If you discover:
credential exposure
authentication bypass
authorization bypass
data leakage
SQL injection
XSS
CSRF
IDOR
privilege escalation
payment vulnerability
secret exposure
do not continue normal implementation as if nothing happened.
Immediately:
1. Stop affected unsafe operation.
2. Identify scope.
3. Document the finding.
4. Fix or contain it.
5. Rotate secrets if required.
6. Add regression tests.
7. Verify the fix.
8. Document the incident appropriately.
65. No Security Theater
Do not claim security merely because a library or middleware exists.
Examples:
JWT exists ≠ authorization is secure
HTTPS exists ≠ application is secure
RBAC table exists ≠ RBAC is enforced
Input schema exists ≠ business validation is complete
Password hashing exists ≠ authentication is secure
Security must be verified through actual behavior and tests.
66. AI-Specific Development Rules
You are an implementation agent, not the product owner.
Do not make product decisions silently.
You may make low-risk implementation decisions when they:
do not change product behavior
do not change security policy
do not change API contracts
do not change business rules
do not change data semantics
For architectural or business decisions, follow the specification or escalate.
67. AI Must Inspect Before Editing
Before modifying an existing file:
Read it.
Understand it.
Inspect related code.
Identify consumers.
Do not overwrite files blindly.
Do not regenerate entire files when a focused modification is sufficient.
68. AI Must Prefer Incremental Changes
Prefer:
small change
→
test
→
verify
→
next change
over:
generate thousands of lines
→
hope it works
Large generated changes must still be divided into logical implementation units.
69. AI Must Not Hide Problems
Never:
disable failing tests
ignore compiler errors
comment out broken code
suppress security warnings without justification
remove validation to make tests pass
delete functionality to avoid errors
unless the task explicitly requires removal and the specification supports it.
70. Verification Requirements
Before marking a task complete, verify:
[ ] Specification requirements satisfied
[ ] Acceptance criteria satisfied
[ ] Code compiles/type-checks
[ ] Formatting passes
[ ] Lint passes
[ ] Tests pass
[ ] Security requirements satisfied
[ ] No secrets committed
[ ] No unrelated files changed
[ ] Documentation updated where required
[ ] Existing functionality remains intact
71. Definition of Done
A feature is NOT "Done" because:
the UI exists
the endpoint responds
the happy path works
the code compiles
A feature is Done only when it satisfies the applicable requirements in:
Section 28 — W8 Definition of Done
including:
Functionality
Security
Validation
Error handling
Loading/empty states
Testing
Accessibility
Localization
RTL/LTR
Observability
Documentation
72. Milestone Completion
A milestone is complete only when:
All required tasks are complete
+
All acceptance criteria pass
+
Required tests pass
+
CI passes
+
Security requirements pass
+
Documentation is updated
Do not mark a milestone complete based on percentage completion.
73. Production Readiness
Never describe W8 as production-ready until the requirements in:
Section 17
Section 20
Section 26
Section 28
Section 29
and all applicable security, testing, deployment, and operational requirements have been verified.
74. Final AI Response Format
After completing a task, report using this structure:
## Task Completed

Task:
<task ID>

Status:
DONE / BLOCKED / PARTIAL

Implemented:
- ...
- ...
- ...

Files Changed:
- ...
- ...

Tests:
- ...

Verification:
- ...

Security:
- ...

Documentation:
- ...

Known Limitations:
- ...

Next Task:
<task ID>
Never claim tests passed if they were not actually executed.
Never claim security verification if it was not performed.
Never claim deployment if deployment was not performed.
75. Golden Rules
The following rules are non-negotiable:
1. Read the specification before implementing.
2. Never invent critical product behavior.
3. Never trust the client for authorization.
4. Never hardcode secrets.
5. Never ship fake functionality.
6. Never ship production mock data.
7. Never ignore errors.
8. Never bypass tests to declare success.
9. Never introduce unnecessary dependencies.
10. Never copy prototype implementation mistakes into production.
11. Never bypass the CMS for production content without authorization.
12. Never silently break API contracts.
13. Never expose sensitive information.
14. Never treat authentication as authorization.
15. Never declare a feature Done before verification.
16. Prefer small, testable, reversible changes.
17. Keep business logic out of presentation layers.
18. Keep security requirements active throughout development.
19. Keep documentation and implementation synchronized.
20. When uncertain about critical behavior, STOP and escalate instead of guessing.
76. W8 Development Principle
Always remember:
MASTER SPECIFICATION
        ↓
IMPLEMENTATION PLAN
        ↓
TASK
        ↓
DESIGN
        ↓
CODE
        ↓
TEST
        ↓
SECURITY VALIDATION
        ↓
CI
        ↓
REVIEW
        ↓
DONE
Never reverse this process.
Do not start with code and invent the specification afterward.
W8 must be built from the specification outward.
END OF AGENTS.md
