# M10 — W8 Notifications, Events & Communication

**Document:** M10_NOTIFICATIONS.md
**Product:** W8
**Milestone:** M10
**Status:** Ready for Implementation
**Priority:** P0
**Depends On:** M0–M9
**Blocks:** M12, M15, M16, M17

---

# 1. Purpose

M10 implements W8's notification and communication infrastructure.

The system must support:

- in-app notifications
- push notifications
- notification preferences
- notification history
- event-driven notifications
- scheduling
- delivery tracking
- retries
- localization
- deep links
- notification deduplication
- user opt-out behavior

---

# 2. Critical Principle

Notifications are an asynchronous system.

Business domains MUST NOT directly call Firebase/APNs/provider SDKs.

Instead:

```text
Domain Event
    ↓
Notification Decision
    ↓
Notification Job
    ↓
Queue
    ↓
Delivery Worker
    ↓
Push Provider
    ↓
Delivery Result
```

---

# 3. Source of Truth

Follow:

- AGENTS.md
- Master Specification
- Sections 06, 07, 10, 14, 15, 17, 20
- M3
- M5
- M6
- M9

---

# 4. Notification Channels

Support only channels defined by the product.

Potential channels:

- in-app
- push
- email

Do not implement additional channels without specification approval.

---

# 5. Notification Types

Potential types:

- workout_reminder
- workout_completed
- nutrition_reminder
- goal_progress
- weekly_summary
- system
- subscription
- security

Exact notification types follow Section 14.

---

# 6. User Preferences

Users must be able to control supported notification categories.

Conceptually:

- Enable all
- Disable all
- Per-category settings

Preference changes must persist server-side.

---

# 7. Permission State

Push permission is different from W8 notification preference.

Model separately:

```text
W8 preference
+
OS permission
```

Possible OS states:

- notDetermined
- granted
- denied
- restricted

Exact platform states depend on the implementation.

---

# 8. Flutter Permission Handling

The mobile app must use the approved platform permission mechanism.

The UI preview/banner is NOT the permission itself.

The app must:

- check permission
- request permission when appropriate
- handle denial
- handle permanent denial
- direct the user to system settings where appropriate

---

# 9. Device Tokens

Device push tokens must be associated with authenticated users.

A user may have multiple devices.

Model:

```text
User
 └── Device
       ├── platform
       ├── token
       ├── app version
       ├── last seen
       └── active status
```

Never assume one user = one device.

---

# 10. Token Security

Push tokens must not be exposed unnecessarily.

Tokens must:

- be validated
- be revocable
- be associated with authenticated accounts
- be removed/deactivated when invalid

---

# 11. Event-Driven Architecture

Notification generation should consume domain/application events.

Examples:

- workout_completed
- goal_progressed
- nutrition_milestone
- subscription_changed
- security_event

Domain code must not contain provider-specific notification logic.

---

# 12. Notification Decision

For each event:

```text
Event
 ↓
Does this event require notification?
 ↓
Is user eligible?
 ↓
Is category enabled?
 ↓
Is OS permission available?
 ↓
Has notification already been sent?
 ↓
Create notification job
```

---

# 13. Deduplication

Notification jobs must be idempotent.

The system must prevent duplicate messages caused by:

- retries
- duplicate events
- worker restart
- network failure

Use a deterministic idempotency key where appropriate.

---

# 14. Scheduling

Scheduled notifications should be created through the backend scheduling system defined by Section 14.

Do not rely exclusively on Flutter timers for important notifications.

---

# 15. Time Zones

User notification scheduling must respect the user's timezone.

Never assume:

Store canonical timestamps in UTC and maintain the user's timezone separately.

---

# 16. Quiet Hours

If supported by Section 14:

Implement:

- timezone
- allowed notification categories

Quiet hours must be enforced server-side for scheduled notifications.

Critical security notifications may follow separate rules defined by the Master Specification.

---

# 17. Localization

Notifications must support:

- Arabic

The notification content should be generated from localized templates.

Do not hardcode notification text in workers.

---

# 18. Template System

Create notification templates.

Conceptually:

- template_key
- locale
- title_template
- body_template
- deep_link
- version
- status

Templates must be validated before publication.

---

# 19. Dynamic Data

Notification templates may receive safe structured variables.

Example:

- user_name
- workout_name
- progress_value

Do not concatenate arbitrary untrusted HTML.

---

# 20. Deep Links

Notifications may open:

- dashboard
- workout
- nutrition
- coach
- history
- specific content

Deep links must be validated.

Never trust arbitrary URLs from notification payloads.

---

# 21. In-App Notification History

The app must provide notification history according to the prototype.

History should support:

- read
- unread
- timestamp
- category
- deep link

Pagination is required for unbounded history.

---

# 22. Read State

Implement:

- mark as read
- mark all as read

Ownership must be enforced.

---

# 23. Delivery Lifecycle

Track states such as:

- created
- queued
- sent
- delivered
- failed
- expired

Exact states follow Section 14.

---

# 24. Retry Policy

Retries must use exponential backoff.

Do not retry permanent failures indefinitely.

Classify failures:

- transient
- permanent
- invalid token
- provider unavailable
- rate limited

---

# 25. Provider Abstraction

Implement a provider abstraction.

Conceptually:

- PushProvider
- EmailProvider

depending on enabled channels.

Business logic must not import provider SDKs directly.

---

# 26. Push Provider

Use the provider selected by the architecture.

The implementation must support the platform requirements for:

- Android
- iOS

including device token handling and notification payloads.

---

# 27. Notification Payload

Payload should contain minimal required information.

Example:

- notification_id
- type
- deep_link
- metadata

Do not put sensitive user data into push payloads.

---

# 28. Notification Security

Protect against:

- notification spoofing
- cross-user notification access
- malicious deep links
- token abuse
- data leakage
- notification flooding

---

# 29. Rate Limiting & Frequency Control

The system must prevent notification spam.

Possible controls:

- per-user frequency limits
- per-category limits
- deduplication
- quiet hours
- cooldowns

Exact thresholds follow Section 14.

---

# 30. Event Reliability

Notification processing must survive:

- worker restart
- queue retry
- provider outage
- duplicate event
- database transaction failure

Use transactional event/outbox patterns where required by the architecture.

---

# 31. Outbox Pattern

Where domain reliability requires it:

```text
Database Transaction
 ├── Business Change
 └── Outbox Event

Worker
 ↓
Publish/Process Event
 ↓
Notification Job
```

This prevents losing events between database commits and message publishing.

---

# 32. Observability

Track:

- notification ID
- event ID
- user/account ID
- channel
- type
- provider
- status
- latency
- failure reason
- retry count

Do not log sensitive notification content unnecessarily.

---

# 33. Admin Integration

Admin Dashboard must eventually allow authorized admins to:

- view notification health
- inspect delivery status
- manage templates
- manage notification categories
- view failure rates

Administrative actions must follow RBAC.

---

# 34. Tests

## Unit

- preference rules
- quiet hours
- deduplication
- template rendering
- retry classification

## Integration

- event → notification
- queue processing
- push token registration
- history
- read state

## Reliability

- worker restart
- duplicate event
- retry
- provider outage

## Security

- cross-user history
- token abuse
- deep-link injection
- unauthorized template modification

---

# 35. Definition of Done

- [ ] Notification domain implemented
- [ ] Notification preferences implemented
- [ ] Device/token management implemented
- [ ] Event-driven architecture implemented
- [ ] Notification decision engine implemented
- [ ] Deduplication implemented
- [ ] Scheduling implemented
- [ ] Timezone handling implemented
- [ ] Quiet hours implemented if required
- [ ] Localization implemented
- [ ] Template system implemented
- [ ] In-app history implemented
- [ ] Read/unread implemented
- [ ] Delivery tracking implemented
- [ ] Retry policy implemented
- [ ] Provider abstraction implemented
- [ ] Deep links secured
- [ ] Outbox/reliability mechanism implemented where required
- [ ] Observability integrated
- [ ] Security tests pass
- [ ] Reliability tests pass
- [ ] CI passes

---

# 36. AI Execution Prompt

Implement W8 M10.

Read:

- AGENTS.md
- Master Specification
- Sections 06, 07, 14, 15, 17, 20
- M0–M9

CRITICAL:

Do not call push providers directly from business domains.
Use events and asynchronous processing.
Separate W8 notification preferences from OS notification permission.
Support multiple devices per user.
Use UTC internally and respect user timezone.
Every notification must be localized.
Every notification operation must be idempotent.
Never put sensitive data into push payloads.
Never trust arbitrary deep links.
Implement retry and failure classification.
Use an outbox pattern where required for reliable event delivery.
Test duplicate events and worker failures.
If notification business rules are ambiguous, STOP before implementation.
