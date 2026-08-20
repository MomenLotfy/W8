تمام. هنكتب Sections 02–05 كحزمة واحدة مترابطة لأنهم أساس بعض:
02 = ماذا يحتوي W8
03 = ما هي البيانات والكيانات التي يحتاجها
04 = كيف يتصرف النظام وما هي قواعد المحتوى
05 = كيف سنبني النظام تقنيا
والأهم: لن نكرر الكلام بين الأقسام. كل requirement له مكان واضح، وباقي الأقسام تشير إليه.
النص التالي جاهز Copy/Paste مباشرة بعد Section 01 داخل:
docs/W8_MASTER_TECHNICAL_SPECIFICATION.md
Section 02 — W8 Product Scope & Complete Feature Specification
2.1 Purpose
This section defines the complete functional scope of W8.
It establishes the product capabilities that MUST be implemented across the Mobile Application, Backend/API Platform, and Admin Dashboard.
This section defines WHAT the system provides.
Detailed domain entities are defined in Section 03.
Detailed business rules and content lifecycle rules are defined in Section 04.
Technical implementation architecture is defined in Section 05.
2.2 Product Surfaces
W8 consists of three primary production surfaces:
W8 Platform
│
├── Mobile Application
│   ├── Onboarding
│   ├── Dashboard
│   ├── Workout
│   ├── Nutrition
│   ├── AI Coach
│   ├── History
│   ├── Notifications
│   ├── Settings
│   ├── Profile
│   └── Subscription / Premium
│
├── Backend/API
│   ├── Identity
│   ├── User Domain
│   ├── Content
│   ├── Workout
│   ├── Nutrition
│   ├── AI
│   ├── Personalization
│   ├── Notifications
│   ├── Payments
│   └── Operations
│
└── Admin Dashboard
    ├── Dashboard
    ├── Users
    ├── Exercises
    ├── Workout Plans
    ├── Nutrition
    ├── AI Insights
    ├── Notifications
    ├── Subscriptions
    ├── Analytics
    └── Settings
2.3 Mobile Application Scope
2.3.1 Authentication
W8 MUST provide:
account registration;
login;
logout;
session management;
password reset;
email/account verification where required;
authentication failure handling;
account lock/rate-limit protections where applicable;
account deletion;
session/device management where supported.
Detailed requirements are defined in Section 15.
2.4 Onboarding
The onboarding flow MUST collect the minimum information required to initialize a user's W8 experience.
The approved prototype contains:
Welcome
Goal
Weight / Height / Target
Activity Level
Training Days / Training Time
Nutrition Preference
Plan Ready
The production implementation MUST preserve the intended UX while validating all submitted values server-side.
Requirements
W8-02-FR-001
The system MUST allow a newly registered user to complete onboarding.
W8-02-FR-002
The system MUST persist onboarding data as structured domain data rather than a single unstructured blob.
W8-02-FR-003
The system MUST validate onboarding values against configured domain constraints.
W8-02-FR-004
The system MUST prevent invalid or contradictory values from being persisted.
W8-02-FR-005
Onboarding completion MUST be idempotent.
W8-02-FR-006
The backend MUST determine whether onboarding is complete.
The client MUST NOT independently assume completion.
2.5 Dashboard
The Dashboard is the user's primary home surface.
It MUST support, as applicable:
daily score;
workout status;
nutrition status;
progress information;
quick actions;
personalized insights;
upcoming/recommended activities;
premium content indicators;
notifications access.
The Dashboard MUST consume backend-provided state and MUST NOT contain hardcoded production business values.
2.6 Workout System
W8 MUST provide a complete workout lifecycle:
Workout Plan
     ↓
Workout Session
     ↓
Exercise
     ↓
Set Logging
     ↓
Session Completion
     ↓
Workout Summary
     ↓
History / Progress
The system MUST support:
workout plans;
workout days;
exercises;
exercise instructions;
sets;
repetitions;
duration;
rest;
weight/resistance where applicable;
workout execution;
workout logging;
workout completion;
workout history;
workout summaries.
Detailed workout rules are defined in Sections 04 and 12.
2.7 Exercise Library
The exercise library MUST support:
exercise name;
description;
instructions;
target muscles;
equipment;
difficulty;
movement/category metadata;
media;
safety/caution information where applicable;
publishing status;
localization;
version/audit information.
Exercises MUST be managed through the CMS.
The production application MUST NOT depend on hardcoded exercise arrays.
2.8 Nutrition System
W8 MUST support structured nutrition content and plans.
The system MUST support, according to the configured nutrition model:
nutrition plans;
meals;
meal items;
ingredients;
nutritional values;
meal timing;
daily plans;
nutrition preferences;
meal replacement;
nutrition history where enabled.
Nutrition data MUST be structured rather than stored solely as formatted text.
2.9 AI Coach
The AI Coach MUST support:
conversational interaction;
context-aware responses;
fitness-related guidance;
approved W8 content retrieval;
personalization context;
quick actions/replies;
conversation history where supported;
safe refusal/escalation behavior;
AI failure handling.
The AI Coach MUST NOT be treated as an unrestricted general-purpose agent.
Detailed AI architecture and rules are defined in Section 13.
2.10 Insights
W8 MAY provide:
daily insights;
weekly summaries;
monthly summaries;
workout insights;
progress insights;
nutrition insights;
behavioral trends.
Insights MUST be generated from defined data sources and business rules.
AI-generated insights MUST be distinguishable internally from deterministic system metrics.
2.11 History
The History surface MUST provide structured access to historical user activity.
It MUST support:
Overview
Aggregated historical metrics.
Workouts
Historical workout sessions and results.
Progress
Progress tracking including supported:
Weight
Strength
Goal progress
The system SHOULD support additional metrics through extensible domain structures.
2.12 Notifications
W8 MUST support:
notification preferences;
notification history;
notification categories;
push notifications;
in-app notifications;
permission state;
notification delivery status where supported;
notification deep links;
notification read/unread state.
Notifications MUST be generated through backend-controlled events and notification policies.
2.13 Settings
Settings MUST provide access to supported:
profile;
language;
theme;
notifications;
account;
subscription;
privacy-related settings;
connected services;
logout;
account deletion where supported.
All settings MUST have explicit persistence behavior.
2.14 Premium / Subscription
W8 MUST support entitlement-based premium access.
Premium functionality MUST be controlled by backend-verified entitlements.
The UI MAY visually lock premium features, but UI state MUST NOT be the authorization mechanism.
Subscription functionality includes:
available plans;
current entitlement;
purchase;
restore;
renewal state;
cancellation state;
expiration;
billing-related information where available.
Detailed requirements are defined in Section 16.
2.15 Admin Dashboard Scope
The Admin Dashboard MUST provide the following primary sections:
Dashboard
Users
Exercises
Workout Plans
Nutrition
AI Insights
Notifications
Subscriptions
Analytics
Settings
Each administrative operation MUST be protected by RBAC.
2.16 Admin Dashboard — Dashboard
The Dashboard MUST provide operational metrics such as:
user counts;
active users;
subscription metrics;
workout activity;
content metrics;
notification metrics;
system health indicators where applicable.
Metrics MUST be generated from real backend data.
2.17 Admin — Users
Authorized administrators MUST be able to:
search users;
filter users;
view user summaries;
inspect account status;
inspect subscription status;
suspend/activate accounts where authorized;
perform approved account operations;
view relevant activity;
access audit history where permitted.
Every destructive or security-sensitive action MUST require confirmation.
2.18 Admin — Content
Administrators MUST be able to manage:
Exercises
create;
edit;
review;
publish;
unpublish;
archive;
search;
filter.
Workout Plans
create;
edit;
compose;
validate;
review;
publish;
unpublish;
archive.
Nutrition
create;
edit;
compose;
validate;
review;
publish;
unpublish;
archive.
Content management is defined in detail in Section 12.
2.19 Admin — Notifications
Authorized administrators MUST be able to:
create notification campaigns where supported;
select target audiences;
schedule notifications;
preview notifications;
monitor delivery;
inspect failures;
cancel scheduled notifications where allowed.
Administrative notification operations MUST be auditable.
2.20 Admin — Analytics
Analytics MUST provide controlled visibility into relevant product metrics.
The Admin Dashboard MUST distinguish between:
operational metrics;
product analytics;
financial/subscription metrics;
system health metrics.
Sensitive information MUST only be visible to roles explicitly authorized to access it.
2.21 Admin — Settings
Admin Settings MAY include:
system configuration;
feature flags;
content configuration;
notification configuration;
AI configuration;
operational configuration.
Security-sensitive settings MUST require appropriate authorization.
2.22 Search & Filtering
Search and filters MUST be functional production features.
They MUST:
use real input controls;
support debouncing where appropriate;
support pagination/incremental loading;
expose empty results;
preserve filter state where appropriate;
validate query parameters server-side.
No production search control MAY remain a visual-only placeholder.
2.23 Universal UI States
Every asynchronous product surface MUST explicitly support:
Loading
Success/Data
Empty
Error
Offline / Network Failure where applicable
These states MUST be represented consistently using the W8 Design System.
2.24 Accessibility
All interactive controls MUST:
have semantic roles;
support keyboard/focus interaction where applicable;
meet minimum touch-target requirements;
expose meaningful labels;
support screen readers;
support RTL;
support dynamic text sizing where applicable.
Charts MUST expose meaningful numerical/semantic information.
2.25 Feature Boundary
Features shown in the prototype but not technically defined MUST NOT be implemented using assumptions.
Before implementation, each such feature MUST have:
a domain model if required;
business rules;
API contract;
state behavior;
error behavior;
authorization behavior;
test requirements.
2.26 Complete Feature Acceptance Principle
A feature is not considered implemented merely because:
a screen exists;
a button works visually;
static data is displayed;
an API returns HTTP 200;
a happy path works.
A production feature MUST satisfy the Definition of Done defined in Section 28.
End of Section 02
Section 03 — W8 Domain & Data Model
3.1 Purpose
This section defines the conceptual domain model for W8.
It establishes:
entities;
relationships;
ownership;
lifecycle;
persistence boundaries;
important invariants.
The database schema MUST be derived from this domain model and MUST NOT be designed solely from UI screens.
3.2 Domain Model Overview
The W8 domain is divided into the following bounded areas:
Identity
│
├── User
├── Account
├── Session
└── Role / Permission

User Profile
│
├── Fitness Profile
├── Goal
├── Preference
└── Activity Profile

Content
│
├── Exercise
├── Workout Plan
├── Workout Day
├── Workout Exercise
├── Nutrition Plan
├── Meal
├── Food Item
└── Educational Content

Execution
│
├── Workout Session
├── Exercise Set Log
├── Nutrition Log
└── Progress Record

AI
│
├── Conversation
├── Message
├── AI Insight
└── Recommendation

Communication
│
├── Notification
├── Notification Preference
└── Notification Delivery

Commerce
│
├── Product/Plan
├── Subscription
├── Entitlement
└── Transaction

Administration
│
├── Admin Action
├── Content Version
└── Audit Event
3.3 Identity Domain
User
Represents the primary W8 end-user identity.
A User MUST have:
unique identifier;
account state;
creation timestamp;
update timestamp.
Authentication credentials MUST be isolated from general profile information.
Account
Represents authentication and account lifecycle state.
Possible states include:
pending
active
suspended
deactivated
deleted
The exact state machine is defined in Section 15.
Session
Represents an authenticated client session.
A session SHOULD contain:
unique identifier;
user reference;
device metadata where appropriate;
creation time;
expiration time;
revocation state.
3.4 User Profile Domain
Fitness Profile
Represents fitness-related attributes required by W8.
Possible attributes include:
height;
weight;
activity level;
training experience;
available training days;
preferred training time;
equipment availability;
relevant preferences.
Values MUST have explicit units.
Goal
Represents a user's fitness objective.
Examples include:
muscle gain;
fat loss;
strength;
general fitness;
maintenance.
The final goal taxonomy MUST be defined in Section 04 and represented as controlled values rather than arbitrary strings.
Preference
Represents user-selected behavior/preferences.
Examples include:
nutrition preference;
language;
theme;
notification preferences;
training preferences.
Preferences MUST NOT be mixed with authoritative account/security state.
3.5 Content Domain
Exercise
An Exercise represents a reusable training movement.
An exercise MAY contain:
id
slug
localized name
description
instructions
target muscles
equipment
difficulty
movement category
media references
safety notes
status
version
created_by
updated_by
timestamps
Workout Plan
A WorkoutPlan represents a structured training program.
It SHOULD reference:
goals;
experience level;
frequency;
duration;
applicable constraints;
workout days.
A WorkoutPlan MUST NOT embed duplicated exercise definitions.
It MUST reference canonical Exercise entities.
Workout Day
Represents a training day within a WorkoutPlan.
A WorkoutDay MUST define an ordered collection of workout exercises.
Workout Exercise
Represents the placement/configuration of an Exercise inside a WorkoutDay.
It MAY define:
order;
sets;
repetitions;
duration;
rest;
target load;
tempo;
notes;
progression rules.
This entity MUST reference the canonical Exercise.
3.6 Nutrition Domain
Nutrition Plan
Represents a structured nutrition program.
It MAY define:
target calories;
macronutrient targets;
dietary preferences;
meal schedule;
duration;
applicable user profiles.
Nutrition plans MUST be versionable where published content can change over time.
Meal
Represents a meal within a Nutrition Plan.
A Meal SHOULD contain:
name;
timing;
ordered food items;
nutritional totals;
preparation instructions where applicable.
Food Item
Represents a reusable food/nutrition item.
Food data MUST be structured so nutritional values can be calculated reliably.
3.7 Execution Domain
Workout Session
Represents an actual workout performed or started by a user.
Possible states:
planned
started
paused
completed
abandoned
cancelled
Exercise Set Log
Represents the user's actual performance for a set.
Possible values include:
repetitions;
weight;
duration;
distance;
perceived effort;
completion state.
The model MUST allow exercise-specific logging without forcing every exercise to use the same measurement model.
Progress Record
Represents a measurable user progress observation.
Examples:
weight;
strength metric;
body measurement;
goal metric.
Progress records MUST contain timestamps and measurement units.
3.8 AI Domain
Conversation
Represents an AI Coach conversation.
A conversation belongs to a user and MAY contain multiple messages.
Message
A message MUST identify:
conversation;
sender type;
content;
timestamp;
processing state where applicable.
Sender types SHOULD include:
user
assistant
system
AI Insight
Represents a generated or calculated insight.
It MUST identify:
user;
insight type;
source;
generation timestamp;
status;
applicable period.
AI insights MUST NOT automatically become authoritative domain state.
Recommendation
Represents a recommendation generated by W8.
A recommendation SHOULD contain:
recommendation type;
source;
reasoning metadata where appropriate;
target user/context;
expiration;
confidence where applicable;
status.
3.9 Communication Domain
Notification
Represents a notification intended for a user or audience.
It SHOULD include:
category;
title;
body;
deep-link metadata;
scheduled time;
status;
creation source.
Notification Preference
Represents whether and how a user receives specific notification categories.
Preferences MUST be evaluated by the notification system before delivery.
Notification Delivery
Represents an attempted delivery through a channel.
Possible channels include:
push;
in-app;
email where later supported.
Delivery status SHOULD distinguish:
queued
sent
delivered
failed
opened
where the channel supports the relevant state.
3.10 Commerce Domain
Subscription
Represents a user's subscription state.
A Subscription MUST NOT itself be treated as the sole authorization mechanism.
Entitlement
Represents the actual access granted to a user.
Example:
premium_ai_coach
premium_workout_plans
premium_nutrition
The entitlement system is authoritative for feature access.
Transaction
Represents a financial transaction or provider event.
Payment provider data MUST be stored according to the payment architecture and MUST NOT expose sensitive payment credentials.
3.11 Administration Domain
Role
Represents an administrative role.
Roles MUST map to explicit permissions.
Permission
Represents an atomic administrative capability.
Examples:
exercise.read
exercise.create
exercise.update
exercise.publish
exercise.archive
user.read
user.suspend
subscription.read
Audit Event
Represents a security or administrative event requiring traceability.
Audit events SHOULD include:
actor;
action;
target;
timestamp;
outcome;
request/correlation identifier;
metadata where safe.
3.12 Content Lifecycle
Content entities that require publication MUST support an explicit lifecycle.
Recommended state model:
draft
in_review
approved
published
unpublished
archived
Only authorized roles may transition content between states.
3.13 Entity Ownership
Every user-owned entity MUST have an explicit ownership relationship.
For example:
User
 ├── Profile
 ├── Goals
 ├── Preferences
 ├── Workout Sessions
 ├── Progress Records
 ├── Conversations
 ├── Notifications
 └── Entitlements
Ownership MUST be validated server-side.
A client-provided user_id MUST NOT be trusted as proof of ownership.
3.14 Data Integrity Rules
W8-03-DATA-001
Foreign-key relationships MUST enforce valid references.
W8-03-DATA-002
Business-critical enumerations MUST use controlled values.
W8-03-DATA-003
Units MUST be explicit for measurable values.
W8-03-DATA-004
Timestamps MUST use a consistent server-side representation, preferably UTC.
W8-03-DATA-005
User-owned records MUST NOT be accessible by another user unless explicitly authorized.
W8-03-DATA-006
Published content MUST remain internally consistent.
W8-03-DATA-007
Deletion behavior MUST be explicitly defined before implementation.
3.15 Versioning
Published content SHOULD support immutable versions or equivalent historical tracking.
When a published workout or nutrition plan changes, the system MUST define whether:
existing users receive the updated version; or
existing assignments remain associated with the previous version.
This decision MUST be explicit per content type.
3.16 Soft Delete vs Hard Delete
Soft deletion SHOULD be preferred for entities requiring:
auditability;
historical reporting;
content recovery;
legal/operational traceability.
Hard deletion MAY be used where required by account deletion/privacy requirements.
The final deletion strategy MUST be defined per entity rather than globally assumed.
3.17 Data Model Rule
The database MUST model domain relationships explicitly.
The following patterns are prohibited:
storing entire relational domains as arbitrary JSON when structured relations are required;
duplicating canonical exercises inside workout plans;
duplicating user identity across unrelated tables;
storing authorization state only in the client;
using display labels as stable identifiers.
End of Section 03
Section 04 — W8 Business Rules & Content Specification
4.1 Purpose
This section defines the behavioral rules governing W8.
The objective is to ensure that W8 behaves predictably regardless of whether the request originates from:
Flutter;
Admin Dashboard;
API client;
background worker;
AI service;
scheduled job.
Business rules MUST be enforced at the authoritative backend/domain layer.
4.2 General Business Rule Principles
W8-04-BR-001
The same business operation MUST produce the same result regardless of which trusted application surface initiates it.
W8-04-BR-002
Client-side calculations MUST NOT replace authoritative server-side calculations.
W8-04-BR-003
Business rules MUST be deterministic unless explicitly defined as AI/recommendation behavior.
W8-04-BR-004
AI output MUST NOT override deterministic safety, entitlement, authorization, or data-integrity rules.
4.3 User Profile Rules
W8-04-BR-005
A user MUST have exactly one active primary account.
W8-04-BR-006
Profile measurements MUST contain valid units.
W8-04-BR-007
The system MUST reject impossible or out-of-configured-range measurements.
W8-04-BR-008
Changing profile information MUST NOT silently rewrite historical workout records.
Historical records represent what occurred at the time.
4.4 Goal Rules
Goals MUST be represented using controlled W8 goal types.
A user MAY have:
one primary active goal;
additional supported secondary goals where explicitly enabled.
Changing a goal MUST NOT automatically delete historical progress.
If the goal change affects recommendations, the personalization engine MUST recalculate applicable recommendations according to defined rules.
4.5 Workout Plan Rules
W8-04-BR-009
A workout plan MUST contain at least one valid workout day before publication.
W8-04-BR-010
Every workout exercise MUST reference an existing valid exercise.
W8-04-BR-011
Archived exercises MUST NOT be added to newly published plans.
W8-04-BR-012
A workout plan MUST pass structural validation before publication.
W8-04-BR-013
A plan MUST NOT contain duplicate ordering positions within the same workout day.
W8-04-BR-014
Workout configuration MUST respect the supported measurement model of each exercise.
4.6 Workout Execution Rules
A user MAY start a workout only if the workout is available to that user.
When a workout begins:
planned → started
A completed workout MUST satisfy the minimum completion criteria defined for that workout.
Completion MUST be persisted server-side.
A failed network request MUST NOT falsely mark the workout as completed.
4.7 Workout Logging Rules
Workout logging MUST support partial progress.
A user MAY:
start;
pause;
resume;
complete;
abandon;
according to the supported session lifecycle.
Duplicate submission of the same set or completion request MUST be handled idempotently.
4.8 Progress Rules
Progress records MUST:
contain timestamps;
contain measurement units;
identify the source;
preserve historical values.
The system MUST NOT overwrite historical records merely because a new value is submitted.
Progress calculations MUST distinguish between:
raw measurement
derived metric
goal progress
4.9 Nutrition Rules
Nutrition content MUST be structured.
A meal MUST reference valid food/nutrition items where applicable.
Nutritional totals SHOULD be calculated from structured ingredient/item values rather than manually trusted aggregate numbers.
If manual override is allowed, it MUST be explicitly represented.
4.10 Nutrition Plan Validation
Before publication, a nutrition plan MUST be validated for:
valid meals;
valid food references;
valid nutritional values;
valid ordering;
required metadata;
configured safety constraints;
applicable dietary tags.
The exact nutrition safety rules MUST be maintained separately from UI logic.
4.11 Content Management Rules
Content MUST follow:
Draft
 ↓
Review
 ↓
Approval
 ↓
Published
 ↓
Unpublished / Archived
Not every content type necessarily requires every state, but the lifecycle MUST be explicit.
W8-04-CONT-001
Only authorized users may create content.
W8-04-CONT-002
Only authorized users may approve content.
W8-04-CONT-003
Only authorized users may publish content.
W8-04-CONT-004
Publishing MUST trigger validation.
W8-04-CONT-005
Invalid content MUST NOT become published.
W8-04-CONT-006
Archiving content MUST NOT silently destroy historical user records.
4.12 Content Version Rules
Published content MUST be treated carefully when changed.
For example:
Exercise v1
     ↓
Published
     ↓
Exercise updated
     ↓
Exercise v2
The system MUST preserve enough historical information to determine what content a user interacted with when required for history/audit purposes.
4.13 Exercise Content Specification
Every production exercise SHOULD define:
Identity
Name
Localized Names
Description
Instructions
Target Muscles
Equipment
Difficulty
Movement Pattern
Media
Safety Notes
Contraindications/Warnings where applicable
Status
Version
Metadata
Content MUST NOT depend solely on images/video to communicate how an exercise is performed.
4.14 Workout Content Specification
A workout plan SHOULD define:
Name
Description
Goal
Level
Frequency
Duration
Workout Days
Exercises
Sets
Repetitions
Duration
Rest
Progression
Notes
Required Equipment
Status
Version
4.15 Nutrition Content Specification
A nutrition plan SHOULD define:
Name
Goal
Dietary Preference
Calories
Macro Targets
Meal Schedule
Meals
Food Items
Quantities
Nutritional Values
Instructions
Status
Version
4.16 CMS Data Entry Principle
The user-provided W8 content dataset will be introduced after the CMS is implemented.
The implementation MUST NOT require developers to manually insert the user's full dataset into PostgreSQL.
The intended workflow is:
CMS
 ↓
Create / Import Content
 ↓
Validate
 ↓
Review
 ↓
Approve
 ↓
Publish
 ↓
Available to Mobile App
The CMS MUST therefore be capable of supporting future ingestion of:
exercises;
workout plans;
nutrition plans;
meals;
supporting content.
4.17 Content Import
If bulk import is implemented, imports MUST:
validate schema;
validate required fields;
detect duplicates;
validate references;
generate an import report;
reject invalid records safely;
avoid partial corruption.
Bulk import MUST NOT automatically publish content unless explicitly authorized.
4.18 Premium Rules
Premium access MUST be determined by entitlement.
The following pattern is prohibited:
if (user.isPremium == true)
as the sole authoritative authorization mechanism.
Instead, access MUST be resolved through the backend entitlement system.
4.19 AI Business Rules
The AI Coach MAY:
explain W8 content;
answer fitness questions within allowed scope;
provide contextual guidance;
recommend approved W8 content;
summarize user activity;
generate supported insights.
The AI MUST NOT:
grant premium access;
change account permissions;
publish content;
modify authoritative workout plans without an authorized workflow;
alter subscription state;
bypass safety rules;
expose private information belonging to another user.
4.20 Recommendation Rules
Recommendations SHOULD consider applicable:
user goal;
experience;
activity level;
training frequency;
available equipment;
preferences;
recent activity;
progress;
content availability;
subscription entitlement.
Recommendations MUST be constrained by:
safety rules;
content publication status;
entitlement;
availability;
user ownership/privacy.
4.21 Recommendation Fallback
If personalization cannot produce a valid recommendation, W8 MUST gracefully fall back to an approved deterministic recommendation or neutral state.
The system MUST NOT fabricate unavailable content.
4.22 Notification Rules
Notifications MUST respect:
user preferences;
permission state;
notification category;
eligibility;
frequency limits;
scheduling;
entitlement where applicable.
A disabled notification category MUST NOT generate a normal delivery request.
4.23 Notification Idempotency
The notification system MUST prevent duplicate delivery caused by:
retries;
worker restarts;
duplicate events;
API retries.
Notification events SHOULD have idempotency identifiers.
4.24 Subscription Rules
Subscription state MUST be synchronized from trusted payment-provider events.
Client-reported payment success MUST NOT independently grant premium access.
Entitlement changes MUST be auditable.
4.25 Account Suspension Rules
Suspended users MUST be prevented from performing operations prohibited by the account state.
The backend MUST enforce suspension.
The mobile client MUST NOT be relied upon to enforce it.
4.26 Error and Conflict Rules
When two operations conflict, the backend MUST return an explicit conflict response where appropriate.
Examples:
publishing already archived content;
modifying an unavailable resource;
duplicate subscription state transition;
invalid content state transition.
4.27 Business Rule Testing
Every critical rule MUST have automated tests covering:
valid case;
invalid case;
boundary case;
unauthorized case;
repeated/idempotent request where applicable;
conflicting state where applicable.
End of Section 04
Section 05 — W8 System Architecture & Technical Architecture
5.1 Purpose
This section defines the production technical architecture of W8.
The architecture MUST support:
security;
modularity;
testability;
scalability;
observability;
maintainability;
controlled deployment;
future AI capabilities;
CMS-driven content;
Android/iOS delivery.
5.2 Architectural Overview
W8 follows a layered, service-oriented modular architecture.
                         ┌──────────────────────┐
                         │      W8 USERS        │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │  Flutter Mobile App  │
                         └──────────┬───────────┘
                                    │ HTTPS
                                    ▼
                         ┌──────────────────────┐
                         │    API / Backend     │
                         │      FastAPI         │
                         └──────────┬───────────┘
                                    │
             ┌──────────────────────┼──────────────────────┐
             │                      │                      │
             ▼                      ▼                      ▼
      ┌─────────────┐       ┌─────────────┐       ┌─────────────┐
      │ PostgreSQL  │       │    Redis    │       │   Workers   │
      │   Primary   │       │ Cache/Queue │       │ Background  │
      └─────────────┘       └─────────────┘       └─────────────┘
             │                      │                      │
             └──────────────────────┼──────────────────────┘
                                    │
                ┌───────────────────┼────────────────────┐
                │                   │                    │
                ▼                   ▼                    ▼
        ┌─────────────┐     ┌─────────────┐      ┌─────────────┐
        │ AI Services │     │ Notifications│      │ Payments    │
        │ / Providers │     │ / Providers │      │ / Providers │
        └─────────────┘     └─────────────┘      └─────────────┘


                         ┌──────────────────────┐
                         │   W8 Admin Dashboard │
                         └──────────┬───────────┘
                                    │ HTTPS
                                    ▼
                              Same Backend
The Admin Dashboard and Mobile Application MUST use the same authoritative backend domain logic.
5.3 Monorepo Architecture
The repository MUST follow the established structure:
w8/
├── AGENTS.md
├── docs/
│   ├── W8_MASTER_TECHNICAL_SPECIFICATION.md
│   ├── design/
│   │   ├── index.html
│   │   └── prototype-audit.md
│   └── implementation/
│       ├── M0_ENGINEERING_FOUNDATION.md
│       ├── M1_BACKEND_FOUNDATION.md
│       └── ...
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
├── .github/
│   └── workflows/
├── .gitignore
├── README.md
└── LICENSE
5.4 Backend Technology
The backend MUST use:
Python
FastAPI
PostgreSQL
SQLAlchemy
Alembic
Redis
Additional technologies MAY be introduced only when justified by an explicit requirement.
The backend MUST use asynchronous I/O where appropriate.
5.5 Mobile Technology
The mobile application MUST use:
Flutter
Dart
The application MUST use a structured state-management architecture.
A single source of truth MUST be maintained for important application state.
The exact state-management library MAY be selected during M0 provided it satisfies the requirements of this specification.
5.6 Admin Technology
The Admin Dashboard MUST be implemented as a production web application.
It MUST:
consume the W8 backend;
support responsive desktop/tablet behavior;
support RBAC;
support localization;
support RTL;
support loading/error/empty states;
support pagination;
support audit-sensitive actions.
The exact frontend framework MAY be finalized during M0, provided it meets the architecture requirements.
5.7 Backend Layering
The backend SHOULD follow:
API / Routes
     ↓
Application / Use Cases
     ↓
Domain
     ↓
Repositories
     ↓
Infrastructure
Responsibilities MUST remain separated.
API Layer
Responsible for:
HTTP;
authentication extraction;
request parsing;
response serialization;
API validation;
error mapping.
Application Layer
Responsible for:
use cases;
orchestration;
transaction boundaries;
authorization coordination.
Domain Layer
Responsible for:
business rules;
domain invariants;
entities;
value objects.
Repository Layer
Responsible for:
persistence abstraction.
Infrastructure Layer
Responsible for:
PostgreSQL;
Redis;
external services;
messaging;
AI providers;
payment providers;
notification providers.
5.8 Database Architecture
PostgreSQL MUST be the authoritative persistent data store for core transactional data.
The database MUST support:
referential integrity;
transactions;
indexes;
constraints;
migrations;
controlled schema evolution.
All schema changes MUST be managed through Alembic migrations.
Manual production schema modification MUST NOT be the normal deployment mechanism.
5.9 Redis
Redis MAY be used for:
caching;
rate limiting;
temporary state;
distributed locks where justified;
job coordination;
short-lived session-related data where appropriate.
Redis MUST NOT become the authoritative permanent store for critical domain data unless explicitly specified.
5.10 Background Processing
Long-running or asynchronous operations SHOULD execute outside the synchronous HTTP request lifecycle.
Examples:
notification delivery;
AI generation;
scheduled insights;
analytics aggregation;
content import;
email delivery;
subscription synchronization;
heavy processing.
Background jobs MUST be idempotent where retries are possible.
5.11 API Architecture
The backend MUST expose versioned APIs.
Example:
/api/v1/
API contracts MUST define:
request;
response;
authentication;
authorization;
validation;
errors;
pagination;
idempotency where required.
Breaking API changes MUST use an explicit versioning/migration strategy.
5.12 Authentication Boundary
Authentication MUST be centralized in the backend identity system.
Clients MAY hold short-lived access credentials and appropriate refresh credentials according to Section 15.
Every protected API request MUST be authenticated.
Every sensitive operation MUST additionally be authorized.
5.13 Authorization Boundary
Authorization MUST occur server-side.
The backend MUST evaluate:
Identity
+
Role / Permission
+
Resource Ownership
+
Entitlement
+
Business Rules
where applicable.
The UI MUST NOT be considered an authorization boundary.
5.14 Content Architecture
Content MUST follow:
Admin CMS
      ↓
Validation
      ↓
Review
      ↓
Approval
      ↓
Publication
      ↓
Backend Content API
      ↓
Mobile Application
This allows W8 to introduce the user's exercise/workout/nutrition datasets after the CMS is operational.
5.15 File / Media Architecture
Media such as:
exercise images;
exercise videos;
profile images;
educational assets;
SHOULD be stored in object storage rather than PostgreSQL binary fields.
The database SHOULD store:
media identifier;
object key;
metadata;
MIME type;
size;
ownership;
lifecycle state.
Public/private access MUST be explicitly defined.
5.16 AI Architecture
The AI layer MUST be isolated behind a W8 AI service boundary.
Mobile/Admin
     ↓
W8 Backend
     ↓
AI Orchestration Layer
     ↓
Context / Retrieval
     ↓
Model Provider
     ↓
Validation / Safety
     ↓
Response
The model provider MUST NOT receive unnecessary private user data.
AI provider integrations MUST be replaceable without rewriting the core domain.
5.17 AI Context
AI context MAY include:
user goals;
relevant preferences;
recent workout activity;
relevant nutrition information;
approved W8 content;
conversation history.
Only the minimum required context SHOULD be provided.
5.18 Recommendation Architecture
Personalization MUST be separated from UI.
User Data
    +
Content
    +
Business Rules
    +
Historical Signals
    ↓
Personalization Engine
    ↓
Candidate Recommendations
    ↓
Safety / Eligibility Validation
    ↓
Final Recommendation
5.19 Notification Architecture
Notifications SHOULD follow an event-driven model:
Domain Event
    ↓
Notification Policy
    ↓
Eligibility
    ↓
Preference Check
    ↓
Scheduling
    ↓
Delivery Worker
    ↓
Provider
The notification system MUST NOT blindly send notifications whenever a UI action occurs.
5.20 Payment Architecture
Payment processing MUST be delegated to approved payment providers.
User
 ↓
Mobile
 ↓
Backend
 ↓
Payment Provider
 ↓
Provider Webhook
 ↓
Backend Verification
 ↓
Subscription State
 ↓
Entitlement
The webhook/provider event MUST be treated as the authoritative trigger for subscription state synchronization.
5.21 Observability Architecture
W8 SHOULD provide:
Application Logs
Metrics
Distributed Traces
Audit Events
Health Checks
Alerts
Every request SHOULD have a correlation/request identifier.
Security-sensitive events MUST be auditable.
5.22 Error Architecture
The backend MUST use a consistent error contract.
Conceptually:
{
  code,
  message,
  details,
  request_id
}
Internal implementation details MUST NOT leak through public error responses.
The exact schema is defined in Section 06.
5.23 Configuration Management
Configuration MUST be environment-driven.
Secrets MUST be provided through:
environment variables;
secret management systems;
deployment configuration.
Secrets MUST NOT be committed to Git.
5.24 Infrastructure Architecture
Infrastructure MUST be reproducible.
Infrastructure-as-Code SHOULD be used for production infrastructure.
The system SHOULD support:
Development
Staging
Production
with isolated configuration and credentials.
5.25 Deployment Architecture
The deployment pipeline SHOULD follow:
Git Push
   ↓
CI
   ↓
Lint
   ↓
Unit Tests
   ↓
Integration Tests
   ↓
Security Scans
   ↓
Build
   ↓
Staging
   ↓
Smoke / E2E Tests
   ↓
Production Approval
   ↓
Production Deployment
Production deployment MUST NOT depend on manually editing application code on servers.
5.26 Security Architecture Principles
The architecture MUST support:
TLS;
secure authentication;
RBAC;
resource authorization;
rate limiting;
input validation;
output sanitization;
secret management;
dependency scanning;
audit logging;
secure headers;
secure storage;
least privilege.
Detailed security architecture is defined in Section 07.
5.27 Scalability Architecture
W8 MUST avoid unnecessary tight coupling between components.
The architecture SHOULD support horizontal scaling of stateless API instances.
Stateful components such as PostgreSQL and Redis MUST have explicit capacity and availability strategies.
Background workers SHOULD scale independently from API instances.
5.28 Reliability Principles
Production systems MUST account for:
network failure;
provider failure;
database failure;
cache failure;
worker failure;
duplicate requests;
partial operations;
deployment failure.
Retries MUST be bounded and SHOULD use exponential backoff where appropriate.
Retries MUST NOT create duplicate business operations.
5.29 Transaction Boundaries
Operations that modify multiple related records MUST define transaction boundaries.
Examples:
Create Workout Plan
     +
Workout Days
     +
Workout Exercises
must either complete atomically or fail safely according to the defined business behavior.
External provider calls MUST NOT be assumed to participate in PostgreSQL transactions.
5.30 Domain Event Principle
Domain events MAY be used for decoupled operations.
Examples:
WorkoutCompleted
SubscriptionUpdated
ContentPublished
UserRegistered
GoalChanged
Events MUST contain sufficient identifiers for downstream processing without exposing unnecessary sensitive data.
Consumers MUST be idempotent.
5.31 Architecture Constraints
The following are prohibited unless explicitly approved:
direct database access from Flutter;
direct database access from Admin;
business logic duplicated independently across clients;
hardcoded production content;
hardcoded secrets;
AI direct database access;
client-controlled premium authorization;
client-controlled role authorization;
unversioned destructive migrations;
unrestricted background retries;
silent fallback to mock data in production.
5.32 Architecture Evolution
The initial implementation SHOULD favor a modular monolith rather than prematurely splitting W8 into independent microservices.
The backend modules MUST have clear boundaries so individual components can later be extracted if scale or organizational requirements justify it.
Initial logical modules SHOULD include:
identity
users
content
workouts
nutrition
progress
ai
personalization
notifications
subscriptions
analytics
audit
admin
A microservice MUST NOT be introduced merely because a domain has its own module.
5.33 Architecture Decision Principle
Every major architectural decision MUST answer:
What problem does it solve?
Why is it needed now?
What alternatives were considered?
What operational cost does it introduce?
How does it affect security?
How does it affect testing?
How does it affect future scalability?
This prevents AI-generated architecture from becoming unnecessarily complex.
5.34 Section 05 Acceptance Criteria
Section 05 is complete when:
Mobile architecture is defined.
Backend architecture is defined.
Admin architecture is defined.
Database responsibility is defined.
Redis responsibility is defined.
Background processing is defined.
API boundary is defined.
Authentication boundary is defined.
Authorization boundary is defined.
CMS architecture is defined.
Media storage strategy is defined.
AI architecture boundary is defined.
Personalization architecture is defined.
Notification architecture is defined.
Payment architecture is defined.
Observability architecture is defined.
Configuration strategy is defined.
Deployment flow is defined.
Security boundaries are defined.
Scalability principles are defined.
Reliability principles are defined.
Transaction boundaries are addressed.
Domain events are addressed.
Prototype implementation is prevented from dictating production architecture.
Premature microservice decomposition is explicitly avoided.
End of Section 05
Sections 02–05 Status
Section
Status
Role
02 — Product Scope & Complete Feature Specification
FINAL
Defines WHAT W8 provides
03 — W8 Domain & Data Model
FINAL
Defines WHAT data W8 owns
04 — W8 Business Rules & Content Specification
FINAL
Defines HOW W8 behaves
05 — W8 System Architecture & Technical Architecture
FINAL
Defines HOW W8 is technically built
Integration order داخل الـMaster:
Section 01 — Product Foundation
        ↓
Section 02 — Product Scope
        ↓
Section 03 — Domain & Data Model
        ↓
Section 04 — Business Rules & Content
        ↓
Section 05 — System Architecture
        ↓
Section 06 — API Contract
        ↓
Section 07 — Security
...
وده مهم جدا: Sections 02–05 دلوقتي بينهم dependency واضحة؛ يعني لما نكمل Section 06 مش هنخترع APIs من الصفر، بل هنشتقها من الـDomain + Business Rules + Architecture اللي ثبتناهم هنا.
