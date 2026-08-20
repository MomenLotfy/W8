تمام. هنمشي كده: كل Section هتكون مستقلة، Final، ومهيأة للدمج مباشرة داخل:
docs/W8_MASTER_TECHNICAL_SPECIFICATION.md
ومش هحط كلام تمهيدي خارج الـSection، عشان تقدر تعمل Copy/Paste بسهولة.
Section 01 — W8 Product Foundation
1.1 Document Purpose
This section establishes the foundational product definition and global principles for W8.
It defines what W8 is, who it serves, the boundaries of the product, the core product principles, terminology, supported platforms, and global constraints that apply across all W8 product, technical, security, data, AI, and operational decisions.
This section is foundational and MUST be interpreted together with all subsequent sections of the W8 Master Technical Specification.
The requirements defined here MUST NOT be silently overridden by implementation code, prototype behavior, framework defaults, or AI-generated assumptions.
1.2 Product Identity
Property
Definition
Product Name
W8
Product Type
Personalized fitness and wellness platform
Primary Client
Mobile application
Administrative Surface
Web-based Admin Dashboard
Primary Mobile Framework
Flutter
Backend
Python / FastAPI
Primary Database
PostgreSQL
API Style
RESTful HTTP API
Supported Mobile Platforms
Android and iOS
Primary Languages
English and Arabic
Layout Direction
LTR and RTL
Themes
Dark and Light
AI Capability
AI Coach and recommendation/personalization services
Content Management
Admin-controlled CMS
Subscription Model
Free/Premium entitlement-based model
W8 is the canonical product name.
All previous prototype references to NUR MUST be treated as legacy naming and MUST NOT appear in production-facing UI, source code identifiers, API contracts, database entities, documentation, or user-facing content unless explicitly required for migration/history purposes.
1.3 Product Definition
W8 is a personalized fitness platform designed to help users plan, execute, monitor, and improve their fitness journey through structured workouts, nutrition guidance, progress tracking, AI-assisted coaching, personalized recommendations, notifications, and subscription-based premium capabilities.
W8 consists of three primary system surfaces:
W8 Mobile Application
W8 Backend/API Platform
W8 Admin Dashboard
These surfaces MUST operate as parts of one coherent system and MUST NOT implement independent or conflicting business logic.
The backend is the authoritative source for business-critical state.
The mobile application and Admin Dashboard are clients of the backend and MUST NOT be treated as trusted sources of business authorization or entitlement decisions.
1.4 Product Vision
W8 aims to provide a reliable, personalized, and secure fitness experience in which users can:
establish fitness goals;
receive appropriate workout and nutrition plans;
execute and log workouts;
track progress;
receive personalized insights;
interact with an AI fitness coach;
manage preferences;
receive relevant notifications;
access premium capabilities according to their entitlements.
Administrators must be able to manage the content and operational aspects of the platform without requiring direct database manipulation.
The system MUST prioritize correctness, safety, privacy, maintainability, and predictable behavior over rapid implementation of visually impressive but unreliable features.
1.5 Product Goals
W8 MUST pursue the following primary goals.
W8-01-G-001 — Personalized Experience
The system MUST provide recommendations and experiences that can be adapted to relevant user attributes, goals, preferences, activity information, and available content.
W8-01-G-002 — Structured Fitness Experience
The system MUST support structured workout and nutrition experiences rather than functioning only as a static content library.
W8-01-G-003 — Progress Tracking
The system MUST allow users to record relevant fitness activity and visualize meaningful progress over time.
W8-01-G-004 — AI-Assisted Coaching
The system MUST provide an AI Coach capable of assisting users within clearly defined W8 capabilities, safety constraints, and available data.
W8-01-G-005 — Content Operations
Authorized administrators MUST be able to create, validate, review, publish, update, unpublish, and archive supported W8 content through the CMS.
W8-01-G-006 — Secure Architecture
Security MUST be treated as a foundational architectural requirement rather than a post-development feature.
W8-01-G-007 — Production Reliability
The platform MUST be designed for failure handling, observability, testing, recovery, and controlled deployment.
W8-01-G-008 — Cross-Platform Delivery
The mobile application MUST support Android and iOS through a shared Flutter codebase unless a platform-specific implementation is explicitly required.
1.6 Product Scope
1.6.1 In Scope
The W8 platform includes:
user authentication and account lifecycle;
user profile and fitness preferences;
onboarding;
dashboard;
workout plans;
exercise library;
workout execution and logging;
workout history;
nutrition plans;
nutrition tracking where specified;
progress tracking;
AI Coach;
AI-generated insights where supported;
personalization;
notifications;
notification preferences;
subscriptions;
premium entitlements;
CMS;
exercise management;
workout-plan management;
nutrition-content management;
Admin Dashboard;
analytics and operational reporting;
audit logging;
observability;
security controls;
localization;
RTL/LTR support;
dark/light themes;
testing and quality controls;
CI/CD and deployment infrastructure.
1.7 Product Boundaries
The following boundaries are mandatory.
W8-01-BND-001
The mobile client MUST NOT directly access the production database.
W8-01-BND-002
The Admin Dashboard MUST NOT directly manipulate production database records outside approved backend services or controlled administrative mechanisms.
W8-01-BND-003
Business-critical authorization MUST be enforced server-side.
W8-01-BND-004
Subscription entitlement decisions MUST be validated by trusted backend services.
W8-01-BND-005
AI-generated content MUST NOT be treated as inherently authoritative merely because it was generated by an AI model.
W8-01-BND-006
User-provided data MUST NOT automatically become trusted W8 content.
W8-01-BND-007
Only approved and published content MUST be exposed to normal end users where the relevant content lifecycle requires publication.
1.8 Target Users
W8 defines the following primary user categories.
1.8.1 End User
The primary consumer of the W8 mobile application.
The End User may:
create and manage an account;
complete onboarding;
define fitness goals;
access assigned or recommended content;
perform workouts;
log workout activity;
access nutrition content;
track progress;
interact with AI Coach;
manage preferences;
manage notification settings;
manage subscription-related functionality according to entitlement.
1.8.2 Administrator
An authorized operational user who manages W8 platform content and operations through the Admin Dashboard.
Administrative permissions MUST be role-based and MUST NOT be granted solely because a user can access the Admin Dashboard.
1.8.3 Content Manager
An administrative role responsible for creating and maintaining fitness and nutrition content where such role is enabled by the RBAC model.
1.8.4 Reviewer / Approver
An administrative role responsible for reviewing content before publication where the content lifecycle requires approval.
1.8.5 System / Service Identity
Non-human identities used by trusted backend services, workers, scheduled jobs, integrations, or infrastructure components.
Service identities MUST follow the same principle of least privilege as human administrative identities.
1.9 Product Surface Boundaries
Mobile Application
The mobile application is responsible for:
presentation;
user interaction;
local UI state;
controlled local caching where applicable;
client-side validation for user experience;
API communication;
secure storage of permitted client-side credentials/tokens;
accessibility;
localization;
device-specific integrations.
The mobile application MUST NOT be considered authoritative for security-sensitive decisions.
Backend/API
The backend is responsible for:
authentication;
authorization;
business rules;
data validation;
persistence;
content lifecycle;
subscription entitlement enforcement;
recommendation orchestration;
AI service orchestration;
notification orchestration;
audit events;
security enforcement;
rate limiting;
API contracts.
Admin Dashboard
The Admin Dashboard is responsible for:
content management;
user administration according to authorized permissions;
subscription and operational visibility;
notification management;
analytics;
system configuration where explicitly supported;
operational workflows.
The Admin Dashboard MUST NOT bypass backend authorization.
1.10 Core Product Principles
W8-01-PR-001 — Security by Default
Every new feature MUST begin with a secure default state.
A feature MUST NOT rely on developers remembering to add security controls later.
W8-01-PR-002 — Least Privilege
Users, administrators, services, workers, and integrations MUST receive only the permissions required to perform their intended operations.
W8-01-PR-003 — Backend Authority
The backend MUST remain authoritative for:
identity;
permissions;
ownership;
entitlements;
content publication state;
business-critical calculations;
security-sensitive operations.
W8-01-PR-004 — Explicit State
Important domain states MUST be explicitly modeled.
The system MUST NOT depend on implicit UI state to represent authoritative business state.
W8-01-PR-005 — Deterministic Business Rules
Business-critical behavior MUST be implemented using explicit, testable rules.
The AI MUST NOT invent business rules at runtime.
W8-01-PR-006 — Fail Securely
When the system cannot safely determine whether an operation is authorized or valid, it MUST fail closed rather than granting access.
W8-01-PR-007 — No Silent Data Mutation
Data MUST NOT be modified silently by background processes, AI services, or UI behavior without an explicitly defined business rule.
W8-01-PR-008 — Auditability
Security-sensitive and administrative actions MUST be traceable through appropriate audit records.
W8-01-PR-009 — Privacy by Design
W8 MUST collect, process, store, and expose only the data required for defined product functionality.
W8-01-PR-010 — Testability
Business-critical functionality MUST be designed so that it can be tested independently from UI rendering.
W8-01-PR-011 — Accessibility
Accessibility MUST be considered part of the product implementation rather than an optional enhancement.
W8-01-PR-012 — Localization First
User-facing strings MUST be localization-ready from the beginning.
English and Arabic MUST use the same localization architecture.
W8-01-PR-013 — No Prototype Leakage
Prototype implementation shortcuts MUST NOT automatically become production architecture.
The prototype defines visual and interaction intent, not production implementation.
1.11 Product Safety Principles
W8 operates in the fitness and nutrition domain. The system MUST therefore distinguish between general fitness guidance and potentially unsafe or medically sensitive recommendations.
W8-01-SAFE-001
W8 MUST NOT represent AI-generated recommendations as professional medical diagnosis or treatment.
W8-01-SAFE-002
AI-generated fitness or nutrition recommendations MUST operate within explicitly defined safety constraints.
W8-01-SAFE-003
The system MUST NOT knowingly recommend content that violates configured safety constraints.
W8-01-SAFE-004
Content published through the CMS MUST pass the required validation/review process before becoming available to end users where the content type requires approval.
W8-01-SAFE-005
Safety-critical rules MUST be enforced independently from the AI model whenever technically possible.
W8-01-SAFE-006
The AI MUST NOT be permitted to directly modify authoritative user, subscription, workout, nutrition, or administrative records without an explicitly authorized backend workflow.
1.12 AI Principles
AI functionality is part of W8 but MUST NOT become an uncontrolled source of product behavior.
W8-01-AI-001
AI services MUST operate within defined system boundaries.
W8-01-AI-002
AI output MUST be validated before being used in business-critical workflows.
W8-01-AI-003
The AI MUST NOT bypass backend authorization.
W8-01-AI-004
The AI MUST NOT directly execute arbitrary database operations.
W8-01-AI-005
The AI MUST NOT invent unavailable W8 content and present it as official W8 content.
W8-01-AI-006
Where recommendations depend on W8 content, the AI SHOULD use approved/published content sources rather than uncontrolled external assumptions.
W8-01-AI-007
AI failures MUST degrade gracefully without breaking unrelated core application functionality.
Detailed AI behavior is defined in Section 13.
1.13 Content Principles
W8 content includes, but is not limited to:
exercises;
exercise instructions;
workout plans;
workout templates;
nutrition plans;
meals;
nutrition information;
educational articles;
AI-supporting knowledge content.
W8-01-CONT-001
Production content MUST have an explicit lifecycle where applicable.
W8-01-CONT-002
Unpublished content MUST NOT be exposed to ordinary end users.
W8-01-CONT-003
Content MUST have an identifiable owner or source.
W8-01-CONT-004
Content changes SHOULD be traceable through versioning or audit history.
W8-01-CONT-005
Initial content entry MUST occur through the W8 Content Management System rather than direct manual production database insertion.
This allows the user's existing exercise, workout-plan, and nutrition datasets to be introduced later through controlled CMS workflows.
1.14 Localization & Internationalization
W8 MUST support:
English;
Arabic;
LTR layouts;
RTL layouts.
W8-01-I18N-001
All user-facing strings MUST be localization resources.
W8-01-I18N-002
Hardcoded user-facing strings MUST NOT be introduced into production Flutter or Admin UI code unless explicitly exempted for technical reasons.
W8-01-I18N-003
RTL behavior MUST be implemented using platform-native directionality mechanisms.
W8-01-I18N-004
Localization MUST apply consistently to the Mobile Application and Admin Dashboard.
W8-01-I18N-005
New supported locales MUST NOT require rewriting business logic.
Detailed localization requirements are defined in Section 24.
1.15 Theming
W8 MUST support:
Dark theme;
Light theme.
The design system MUST use centralized typed design tokens.
Production widgets MUST NOT introduce arbitrary raw color values, spacing values, radius values, or typography definitions when an equivalent W8 design token exists.
The approved prototype's visual language MUST be treated as the starting reference for the production design system.
Detailed implementation requirements are defined in Section 25.
1.16 Prototype Authority
The approved prototype is located at:
docs/design/index.html
The prototype is a visual and interaction reference only.
It MAY be used to determine:
intended layouts;
visual hierarchy;
navigation intent;
component appearance;
interaction concepts;
screen relationships.
It MUST NOT be used as the authority for:
backend architecture;
database schema;
API security;
authentication;
authorization;
production state management;
payment processing;
AI architecture;
infrastructure;
production data handling.
The prototype audit is retained separately as design/UX historical context.
1.17 Global Technical Principles
W8-01-TECH-001
Production code MUST be modular and maintainable.
W8-01-TECH-002
Business logic MUST NOT be tightly coupled to presentation widgets.
W8-01-TECH-003
External integrations MUST be isolated behind explicit interfaces where appropriate.
W8-01-TECH-004
Secrets MUST NOT be hardcoded into source code.
W8-01-TECH-005
Production credentials MUST NOT be committed to Git.
W8-01-TECH-006
API contracts MUST be explicit and versionable.
W8-01-TECH-007
Database migrations MUST be version-controlled.
W8-01-TECH-008
Destructive database changes MUST require explicit migration handling.
W8-01-TECH-009
Dependencies MUST be pinned or constrained appropriately and regularly reviewed.
W8-01-TECH-010
Production behavior MUST NOT depend on mock data.
W8-01-TECH-011
Temporary development implementations MUST be explicitly identified and MUST NOT silently enter production.
W8-01-TECH-012
The system MUST provide structured logging and observability for production-critical operations.
1.18 Global Data Principles
W8-01-DATA-001
Every persistent domain entity MUST have a clearly defined ownership and lifecycle model.
W8-01-DATA-002
Sensitive data MUST be protected both in transit and at rest according to the applicable security architecture.
W8-01-DATA-003
Data validation MUST occur at the API boundary and, where appropriate, at the domain layer.
W8-01-DATA-004
Client-side validation MUST NOT replace server-side validation.
W8-01-DATA-005
The system MUST maintain referential and domain integrity.
W8-01-DATA-006
Deletion behavior MUST be explicitly defined for each domain entity.
W8-01-DATA-007
Data retention MUST be defined according to the type and purpose of the data.
Detailed data architecture and lifecycle rules are defined in Sections 03, 08, 15, 17, and 21.
1.19 Global Error Handling Principles
W8 MUST distinguish between:
validation errors;
authentication errors;
authorization errors;
not-found conditions;
conflict conditions;
rate-limit conditions;
dependency failures;
network failures;
unexpected internal failures.
User-facing error messages MUST be safe and understandable.
Internal errors MUST NOT expose:
stack traces;
secrets;
database credentials;
internal infrastructure details;
sensitive identifiers;
implementation-specific security information.
1.20 Global Observability Principles
Production-critical operations MUST be observable.
The system SHOULD provide sufficient telemetry to determine:
whether a request succeeded;
why a request failed;
where a failure occurred;
which service was involved;
whether the issue is isolated or systemic;
whether user impact occurred.
Logs, metrics, traces, and audit events MUST be treated as separate concepts where appropriate.
Detailed observability requirements are defined in Section 17.
1.21 Global Testing Principles
W8 MUST use multiple levels of testing.
These include, as applicable:
unit tests;
integration tests;
API tests;
database tests;
authorization tests;
security tests;
widget/UI tests;
end-to-end tests;
regression tests;
performance tests.
Critical business rules MUST have automated test coverage.
A feature MUST NOT be considered complete solely because its UI works manually.
1.22 Global Environment Principles
W8 MUST distinguish between at least:
Development
Staging
Production
Environment-specific configuration MUST NOT be hardcoded into application logic.
Production credentials and infrastructure configuration MUST remain isolated from development environments.
1.23 Global AI Development Constraints
AI coding agents working on W8 MUST follow these rules:
The Master Technical Specification is the authoritative product and technical requirement source.
AGENTS.md defines how the AI MUST work.
The current implementation milestone defines the authorized implementation scope.
The AI MUST inspect existing code before creating or modifying files.
The AI MUST NOT rewrite working architecture without a documented reason.
The AI MUST NOT introduce mock production behavior.
The AI MUST NOT invent unspecified business rules.
The AI MUST NOT weaken security to make implementation easier.
The AI MUST NOT bypass tests.
The AI MUST NOT silently change API contracts.
The AI MUST NOT silently change database models or migrations in unrelated tasks.
The AI MUST keep changes scoped to the current task.
The AI MUST run the relevant validation and test suite after implementation.
The AI MUST report failures rather than hiding or bypassing them.
The AI MUST preserve existing functionality unless the current specification explicitly changes it.
Detailed AI operating rules are defined in Section 27 and AGENTS.md.
1.24 Non-Goals
The following are not automatically part of W8 unless explicitly introduced through a future specification change:
medical diagnosis;
emergency medical services;
replacement of licensed medical professionals;
unrestricted AI-generated medical advice;
arbitrary user-created public content;
unrestricted social networking;
direct database access from mobile clients;
unreviewed production content;
arbitrary third-party integrations;
enterprise SSO;
desktop end-user application;
unrestricted AI agent autonomy.
A future feature MAY be added through the formal specification/change process.
1.25 Source-of-Truth Hierarchy
W8 documentation and implementation artifacts MUST follow this hierarchy.
Level 1 — AGENTS.md
Defines HOW the AI MUST work.
It controls:
engineering workflow;
coding behavior;
repository rules;
validation requirements;
change discipline.
Level 2 — W8_MASTER_TECHNICAL_SPECIFICATION.md
Defines WHAT W8 MUST be.
It is the canonical source for:
product requirements;
domain rules;
architecture;
security requirements;
APIs;
data contracts;
technical constraints;
acceptance requirements.
Level 3 — Implementation Milestones
Located under:
docs/implementation/
These define:
implementation order;
milestones;
tasks;
task dependencies;
implementation-specific execution details.
Implementation documents MUST NOT silently override the Master Specification.
Level 4 — Prototype
Located at:
docs/design/index.html
The prototype defines:
visual reference;
interaction reference;
intended UX behavior.
It does not define production architecture.
Level 5 — Audit / Historical Material
Audit reports and research documents provide:
findings;
rationale;
identified gaps;
historical context.
They MUST NOT override the approved Master Specification.
1.26 Requirement Priority
When requirements conflict, the following priority applies:
Explicit security and safety requirements
Explicit product/business requirements
Explicit domain/data integrity requirements
Explicit API and architectural contracts
UX/UI requirements
Prototype implementation behavior
Framework defaults
AI assumptions
The AI MUST NOT resolve a conflict by silently choosing one interpretation.
A genuine unresolved conflict MUST be reported.
1.27 Requirement Language
The following terminology is normative:
Keyword
Meaning
MUST
Mandatory requirement
MUST NOT
Prohibited behavior
SHOULD
Recommended unless a justified reason exists
SHOULD NOT
Discouraged unless a justified reason exists
MAY
Optional
MUST be tested
Automated or explicitly defined verification is required
Requirements written using MUST/MUST NOT are binding implementation requirements.
1.28 Requirement ID Convention
All normative requirements in the Master Specification MUST use stable identifiers.
General format:
W8-{SECTION}-{TYPE}-{NUMBER}
Examples:
W8-01-G-001
W8-01-BND-001
W8-01-SEC-001
W8-15-FR-001
W8-12-DATA-001
W8-16-TEST-001
Requirement IDs MUST NOT be casually renumbered after implementation begins.
If a requirement becomes obsolete, it SHOULD be marked deprecated rather than silently reused for a different requirement.
1.29 Change Management
The W8 Master Specification is expected to evolve during development.
Any change that affects:
product scope;
business rules;
security;
data model;
API contracts;
subscription behavior;
AI behavior;
user flows;
infrastructure;
compliance requirements;
MUST be treated as a specification change.
Changes MUST be reviewed for their impact on:
existing implementation;
database migrations;
APIs;
tests;
security;
documentation;
implementation milestones.
The implementation MUST NOT silently diverge from the specification.
1.30 Section 01 Acceptance Criteria
Section 01 is considered complete when:
W8 product identity is explicitly defined.
Product purpose and goals are explicit.
Product boundaries are defined.
Primary user categories are defined.
Mobile, Backend, and Admin responsibilities are separated.
Security principles are established.
AI boundaries are established.
Content principles are established.
Localization requirements are established.
Prototype authority is explicitly limited.
Global technical principles are defined.
Global data principles are defined.
Error handling principles are defined.
Observability principles are defined.
Testing principles are defined.
Environment principles are defined.
AI development constraints are established.
Non-goals are explicitly stated.
Source-of-truth hierarchy is defined.
Requirement priority is defined.
Requirement ID convention is defined.
Change management is defined.
No critical product or architectural ambiguity remains within the scope of this section.
1.31 Dependencies
This section establishes foundational principles consumed by all subsequent sections.
Primary dependencies:
Section 02 — Product Scope & Complete Feature Specification
Section 03 — W8 Domain & Data Model
Section 04 — W8 Business Rules & Content Specification
Section 05 — W8 System Architecture & Technical Architecture
Section 07 — Security Architecture, Threat Model & RBAC
Section 27 — AI Development Rules
Section 28 — Definition of Done
Section 29 — Implementation Milestones
All subsequent sections MUST remain consistent with the principles defined here.
Section 01 Status
Status: FINAL
Version: 1.0.0
Ready for inclusion in: W8_MASTER_TECHNICAL_SPECIFICATION.md
Prototype Reference: docs/design/index.html
Product Name: W8
Legacy Product Name: NUR — deprecated for production use
