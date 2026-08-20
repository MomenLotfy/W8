# M4 — W8 Content Management System

**Document:** M4_CONTENT_MANAGEMENT.md
**Product:** W8
**Milestone:** M4
**Status:** Ready for Implementation
**Priority:** P0 — Critical
**Depends On:** M0, M1, M2, M3
**Blocks:** M5, M6, M8, M9, M12

---

# 1. Purpose

M4 creates the W8 Content Management System (CMS).

This milestone is intentionally implemented BEFORE importing the real W8
exercise library, workout plans, nutrition plans, meals, articles, or AI
knowledge content.

The CMS becomes the controlled source through which authorized administrators
create, validate, review, publish, update, archive, and version W8 content.

The goal is to prevent hardcoded content and manual database editing.

---

# 2. Critical Principle

W8 content MUST NOT be embedded directly inside:

- Flutter widgets
- API route handlers
- business logic
- SQL migration files
- hardcoded JSON inside application source

Production content must be managed through the CMS and persisted through
the backend/domain model.

---

# 3. Source of Truth

Follow:

1. `/AGENTS.md`
2. Master Specification
3. Section 03 — Domain & Data Model
4. Section 04 — Business Rules & Content
5. Section 06 — API Contract
6. Section 07 — Security/RBAC
7. Section 11 — Admin Dashboard
8. Section 12 — Content Management & Builders
9. Section 13 — AI/Personalization
10. This document

---

# 4. CMS Objectives

Implement the foundation for managing:

```text
Exercises
Exercise media
Muscle groups
Equipment
Exercise categories
Workout plans
Workout templates
Workout blocks
Nutrition plans
Meals
Meal components
Nutrition targets
Articles
AI knowledge content where specified
Tags
Categories
Content metadata
Content versions
```

Only entities explicitly defined by the Master Specification should be created.

---

# 5. Content Lifecycle

Every publishable content entity must support a controlled lifecycle.

Conceptually:

```text
Draft
  ↓
In Review
  ↓
Approved
  ↓
Published
  ↓
Archived
```

The exact statuses and transitions must follow Section 12.

Invalid transitions MUST be rejected server-side.

---

# 6. Content Ownership

CMS content belongs to the W8 platform, not individual mobile users.

Therefore:

- normal users cannot create production content
- normal users cannot publish content
- normal users cannot modify CMS records
- only authorized admin roles can perform CMS operations

RBAC MUST be enforced server-side.

---

# 7. Task List

## M4-T01 — CMS Architecture

Create the CMS domain boundary.

CMS logic MUST NOT be embedded in Admin UI code.

Admin UI calls APIs.

APIs call application services.

Application services operate on domain models.

## M4-T02 — Content Metadata

Define common content metadata where applicable:

- id
- status
- version
- created_at
- updated_at
- created_by
- updated_by
- published_at
- published_by
- archived_at

Do not duplicate metadata unnecessarily across unrelated entities.

## M4-T03 — Exercise Taxonomy

Implement canonical exercise taxonomy.

Potential entities:

- muscle group
- secondary muscle
- equipment
- movement pattern
- difficulty
- exercise category

Only values approved by the Master Specification may become canonical enums.

## M4-T04 — Exercise Entity

Implement the exercise model.

An exercise should support structured attributes such as:

- name
- localized name
- description
- instructions
- muscles
- equipment
- difficulty
- movement pattern
- exercise type
- media references
- safety notes
- status
- version

Exact fields MUST follow Section 03/12.

Do not use one giant free-form JSON object as the only representation.

## M4-T05 — Exercise Localization

Exercise content must support English and Arabic where required.

Localization MUST NOT be implemented as duplicated unrelated records.

Use the localization strategy defined in the Master Specification.

## M4-T06 — Exercise Media

Media references must be modeled separately from exercise metadata.

The CMS must support:

- image
- video
- thumbnail
- instructional media

where specified.

Store references/metadata rather than embedding large binary data inside PostgreSQL.

## M4-T07 — Media Validation

Media uploads must validate:

- MIME type
- file size
- extension
- dimensions where required
- safe filename handling
- storage destination

Never trust client-provided MIME types alone.

## M4-T08 — Workout Content Models

Create the content structures required by the Workout Builder.

The content layer must support composition without duplicating exercises.

Conceptually:

```text
Workout Plan
    ↓
Workout Session
    ↓
Exercise Prescription
    ↓
Exercise
```

The exact hierarchy must follow Section 12.

## M4-T09 — Nutrition Content Models

Create content structures required by the Nutrition Builder.

Conceptually:

```text
Nutrition Plan
    ↓
Meal
    ↓
Food/Meal Item
    ↓
Nutrition values
```

Exact relationships must follow Section 12.

## M4-T10 — Article/Knowledge Content

Implement article/content entities if required.

Content must support:

- title
- localized content
- category
- tags
- status
- author/editor
- publication metadata
- version

## M4-T11 — Draft Management

Authorized editors must be able to:

- create draft
- edit draft
- save draft
- preview draft

Draft content MUST NOT automatically become visible to mobile users.

## M4-T12 — Review Workflow

Implement:

- Draft → Review
- Review → Approved
- Approved → Published

according to permissions defined by Section 07/12.

Editors MUST NOT bypass approval if their role is not allowed to publish.

## M4-T13 — Publishing

Publishing must be an explicit action.

Requirements:

- authorization
- validation
- transaction
- version creation
- publication timestamp
- publisher identity
- audit event

Publishing invalid content MUST fail.

## M4-T14 — Versioning

Published content MUST be version-aware.

A future edit must not silently mutate the historical published version when version history is required.

The system must allow the platform to identify which content version was published at a given time.

## M4-T15 — Archive

Implement archive behavior.

Archived content:

- must not appear in new user-facing content queries
- must remain recoverable where required
- must preserve historical references where required

Do not hard-delete content that is referenced by historical workout records unless the lifecycle specification explicitly permits it.

## M4-T16 — Search

CMS search must be real.

Implement:

- text query
- filters
- pagination
- deterministic ordering

No static search placeholders.

## M4-T17 — Filtering

Support appropriate filters for:

- status
- category
- difficulty
- equipment
- muscle group
- language
- author
- updated date

Only filters relevant to each content type should appear.

## M4-T18 — Validation Engine

CMS validation must run before publishing.

Examples:

**Exercise:**

- required name
- valid category
- valid difficulty
- valid media
- valid muscle references

**Workout:**

- valid exercise references
- valid set/rep configuration
- valid progression configuration

**Nutrition:**

- valid meal references
- valid nutrition values
- valid plan structure

---

# 8. Content Import Strategy

Real user-provided content will be introduced AFTER the CMS exists.

The correct process is:

```text
Raw content
    ↓
Normalization
    ↓
Validation
    ↓
Import staging
    ↓
Human review
    ↓
CMS draft
    ↓
Approval
    ↓
Publish
```

Do NOT directly insert the user's exercise/workout/nutrition data into production tables manually.

---

# 9. Import Pipeline

Create an import architecture capable of handling:

- CSV
- JSON
- structured spreadsheets

where appropriate.

Importers MUST:

- validate schema
- detect duplicates
- validate references
- produce an import report
- support dry-run
- avoid partial corruption
- identify failed records

---

# 10. Duplicate Detection

Content imports must detect potential duplicates.

Examples:

Two exercises with the same canonical identity should not silently become two unrelated exercises.

Use stable identifiers where the source data provides them.

Never use exercise name alone as a guaranteed unique identifier unless the Master Specification explicitly defines it as unique.

---

# 11. Admin CMS Requirements

The Admin Dashboard must provide appropriate CRUD workflows:

- List
- Create
- Edit
- Preview
- Review
- Approve
- Publish
- Archive
- Version history

Delete operations must use confirmation dialogs.

Destructive operations require appropriate RBAC.

---

# 12. Audit Requirements

Audit:

- content created
- content edited
- content submitted for review
- content approved
- content published
- content archived
- content restored
- content deleted
- bulk import
- bulk update

Audit records must identify:

- actor
- action
- entity
- timestamp
- request ID
- result

Do not log full sensitive payloads unnecessarily.

---

# 13. API Requirements

CMS APIs require:

- authentication
- role/permission enforcement
- validation
- pagination
- filtering
- consistent errors
- audit events

Never expose draft content to normal mobile users.

---

# 14. Mobile Content Rules

The mobile application must consume only:

- published
- valid
- available

content.

Mobile APIs MUST NOT return arbitrary drafts.

---

# 15. Caching

Published content may be cached.

However:

- drafts MUST NOT leak through cache
- cache invalidation must happen after publication changes
- permissions must not be bypassed by cached responses

---

# 16. Security Requirements

CMS is a high-impact administrative surface.

Protect against:

- unauthorized publishing
- privilege escalation
- IDOR
- mass assignment
- unsafe uploads
- malicious file types
- content injection
- stored XSS where HTML is supported
- draft leakage
- version manipulation
- bulk-operation abuse

---

# 17. Tests

Required test categories:

**Permissions**

- editor can edit allowed content
- unauthorized user rejected
- reviewer permissions enforced
- publisher permissions enforced

**Lifecycle**

- valid transitions
- invalid transitions
- publish validation
- archive behavior

**Content**

- exercise validation
- workout validation
- nutrition validation

**Import**

- valid import
- invalid row
- duplicate
- missing reference
- dry run
- partial failure handling

**Security**

- IDOR
- mass assignment
- unauthorized publish
- unsafe upload

---

# 18. Definition of Done

- [ ] CMS architecture implemented
- [ ] Exercise model implemented
- [ ] Exercise taxonomy implemented
- [ ] Media model implemented
- [ ] Workout content models implemented
- [ ] Nutrition content models implemented
- [ ] Localization strategy implemented
- [ ] Draft workflow implemented
- [ ] Review workflow implemented
- [ ] Publishing implemented
- [ ] Versioning implemented
- [ ] Archive implemented
- [ ] Search implemented
- [ ] Filtering implemented
- [ ] Validation implemented
- [ ] Import architecture implemented
- [ ] RBAC enforced
- [ ] Audit events implemented
- [ ] Admin CMS APIs implemented
- [ ] Tests pass
- [ ] Security tests pass
- [ ] CI passes
- [ ] No production content is hardcoded

---

# 19. AI Execution Prompt

You are implementing W8 Milestone M4 — Content Management System.

Read:

- /AGENTS.md
- Master Specification
- Sections 03, 04, 06, 07, 11, 12 and 13
- M0
- M1
- M2
- M3

CRITICAL:

Do NOT import the user's real exercise, workout, or nutrition data yet.

First build the CMS and import pipeline.

Do not insert production content through SQL scripts.

Do not hardcode content into Flutter.

Do not bypass approval/publishing workflows.

Every content mutation must be authorized server-side.

Every publish operation must validate the complete content graph.

Every upload must be validated.

Every destructive operation must be audited.

After each task:

- run tests
- inspect migrations
- run security checks
- inspect authorization
- inspect API responses
- inspect git diff

If the content model is ambiguous, stop before creating irreversible schema assumptions.
