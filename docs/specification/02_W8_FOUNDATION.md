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
using display labels as stable ide
