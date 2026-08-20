# W8 — M11 Payments, Subscriptions & Entitlements

Status: Implementation Specification
Milestone: M11
Depends On:
- M0 Engineering Foundation
- M1 Backend Foundation
- M2 Identity
- M3 Core User Domain
- M4 Content Management
- M5 Workout Engine
- M6 Nutrition Engine
- M7 Mobile Core UI
- M8 AI Coach
- M9 Personalization

Primary Goal:
Implement a secure, provider-agnostic subscription and entitlement system.

---

## 1. Objective

W8 must support premium subscriptions without coupling business logic directly to a payment provider.

The payment provider is responsible for:
- Charging the customer
- Subscription lifecycle
- Payment status
- Store/platform billing

W8 backend is responsible for:
- Mapping provider subscriptions to W8 users
- Maintaining entitlement state
- Enforcing premium access
- Processing webhook events
- Preventing duplicate processing
- Maintaining subscription history
- Handling expiration, cancellation, grace periods and refunds

The mobile application must NEVER determine premium access solely from local state.

Backend authorization is authoritative.

---

## 2. Core Principles

1. Payment provider is external.
2. Backend is authoritative for entitlements.
3. Client is never trusted for subscription status.
4. Webhooks are the primary source of lifecycle updates.
5. Webhook processing must be idempotent.
6. Every financial event must be auditable.
7. No card/payment credentials are stored by W8.
8. Secrets must never exist in source code.
9. Premium features must fail closed.
10. Subscription logic must be provider-agnostic.

---

## 3. Supported Subscription Concepts

W8 must support:

- Free user
- Premium monthly
- Premium yearly
- Trial if enabled
- Active subscription
- Cancelled subscription
- Expired subscription
- Grace period
- Paused subscription if provider supports it
- Refunded subscription
- Revoked entitlement

Exact products/prices must be configurable rather than hardcoded.

---

## 4. Domain Entities

### Subscription

Fields:

- id
- user_id
- provider
- provider_customer_id
- provider_subscription_id
- product_id
- plan_code
- billing_interval
- status
- current_period_start
- current_period_end
- cancel_at_period_end
- cancelled_at
- ended_at
- trial_start
- trial_end
- created_at
- updated_at

---

### Entitlement

Fields:

- id
- user_id
- entitlement_code
- source
- status
- starts_at
- expires_at
- created_at
- updated_at

Example:

`premium_ai_coach`

`premium_workout_plans`

`premium_nutrition`

`premium_insights`

`premium_content`

---

### Payment Event

Fields:

- id
- provider
- provider_event_id
- event_type
- received_at
- processed_at
- processing_status
- payload_hash
- error_code
- retry_count

Never store unnecessary sensitive payment information.

---

## 5. Subscription State Machine

Allowed states:

FREE

TRIALING

ACTIVE

PAST_DUE

GRACE_PERIOD

CANCELLED

EXPIRED

REFUNDED

REVOKED

Transitions must be explicit.

Example:

```text
ACTIVE
→ CANCELLED
→ EXPIRED

ACTIVE
→ PAST_DUE
→ GRACE_PERIOD
→ EXPIRED

ACTIVE
→ REFUNDED
```

Invalid state transitions must be rejected or safely ignored.

---

## 6. Entitlement Resolution

Create one backend service:

`EntitlementService`

Responsibilities:

- Resolve user entitlements
- Check active subscription
- Apply expiration rules
- Handle grace period
- Handle revocation
- Return normalized entitlement response

Example:

GET `/v1/me/entitlements`

Response concept:

```json
{
  "plan": "premium",
  "status": "active",
  "entitlements": [
    "premium_ai_coach",
    "premium_workout_plans",
    "premium_nutrition",
    "premium_insights"
  ],
  "expires_at": "..."
}
```

---

## 7. Premium Authorization

Every premium backend endpoint must enforce entitlement authorization.

Example:

```text
Request
  ↓
Authentication
  ↓
User identity
  ↓
Entitlement check
  ↓
Business logic
```

Never:

```text
Flutter checks premium
↓
API trusts Flutter
```

---

## 8. Webhooks

Webhook endpoint:

`POST /v1/webhooks/{provider}`

Requirements:

- Signature verification
- Timestamp validation if supported
- Replay protection
- Idempotency
- Event persistence
- Transactional processing
- Safe retries
- Structured logging

Processing flow:

```text
Receive webhook
↓
Verify signature
↓
Extract event ID
↓
Check duplicate
↓
Persist event
↓
Process event
↓
Update subscription
↓
Update entitlement
↓
Mark event processed
```

---

## 9. Idempotency

The same webhook may arrive multiple times.

Unique constraint:

`provider + provider_event_id`

If already processed:

Return success without repeating side effects.

---

## 10. Client Behavior

Mobile must display:

- Current plan
- Subscription status
- Expiration date
- Renewal state
- Upgrade CTA
- Manage subscription CTA
- Restore purchases where applicable

The UI must refresh entitlement state after purchase.

---

## 11. Purchase Flow

Generic flow:

```text
User opens Premium
↓
Fetch products
↓
Display current prices
↓
User selects product
↓
Payment provider flow
↓
Provider confirms transaction
↓
Backend receives provider event
↓
Backend updates subscription
↓
Backend grants entitlement
↓
Mobile refreshes entitlements
```

Do not unlock premium permanently based only on client purchase callback.

---

## 12. Restore Purchases

Restore must query the provider and reconcile with backend state.

Flow:

```text
Restore
↓
Provider verification
↓
Backend reconciliation
↓
Subscription update
↓
Entitlement update
```

---

## 13. Cancellation

Cancellation does not necessarily mean immediate entitlement removal.

If:

`cancel_at_period_end = true`

the user remains premium until:

`current_period_end`

unless provider reports immediate revocation/refund.

---

## 14. Refunds

Refund event must trigger appropriate entitlement behavior.

The exact policy must be configurable.

Default:

- Refund detected
- Subscription marked refunded
- Entitlement revoked if provider indicates access termination
- Audit event generated

---

## 15. Admin

Admin must be able to:

- View subscriptions
- Search users
- Filter by status
- View subscription history
- View entitlement state
- Grant promotional entitlement
- Revoke promotional entitlement
- View payment event status

Admin must NOT be able to modify provider financial records.

Every manual entitlement change requires:

- Admin identity
- Reason
- Timestamp
- Previous state
- New state

---

## 16. Security

Never store:

- Card numbers
- CVV
- Payment passwords
- Provider private credentials in DB

Webhook secrets must be stored in secret management.

Never log raw webhook secrets.

Never expose provider credentials to Flutter.

---

## 17. Failure Handling

Payment provider unavailable:

- Do not crash application
- Keep last verified entitlement
- Mark provider sync as pending where appropriate
- Retry asynchronously

Webhook processing failure:

- Persist event
- Mark failed
- Retry
- Alert after retry threshold

---

## 18. Testing

Required tests:

### Unit

- State transitions
- Entitlement calculation
- Expiration
- Grace period
- Cancellation
- Refund
- Idempotency

### Integration

- Webhook verification
- Subscription creation
- Subscription renewal
- Cancellation
- Refund
- Restore

### Security

- Invalid webhook signature
- Replay attack
- Duplicate event
- Unauthorized entitlement modification
- User attempting to access premium without entitlement

### E2E

- Free → Premium
- Premium → Cancelled
- Premium → Expired
- Restore purchase

---

## 19. Definition of Done

M11 is complete only when:

- Subscription model exists
- Entitlement model exists
- State machine is enforced
- Provider abstraction exists
- Webhook verification exists
- Webhooks are idempotent
- Premium endpoints enforce entitlements
- Restore flow exists
- Cancellation flow exists
- Refund behavior exists
- Admin subscription management exists
- Audit logging exists
- Secrets are externalized
- Tests pass
- No payment credentials are stored
- Staging payment flow works
