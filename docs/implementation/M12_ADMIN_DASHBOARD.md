# W8 — M12 Admin Dashboard

Status: Implementation Specification
Milestone: M12

---

## 1. Objective

Build a production-grade administrative control plane for W8.

The Admin Dashboard is not a second version of the mobile app.

It is an operational system for:

- User management
- Content management
- Workout management
- Nutrition management
- AI management
- Notifications
- Subscriptions
- Analytics
- Audit
- System configuration

---

## 2. Architecture

Admin:

- Separate frontend application
- Separate route namespace
- Same backend API
- RBAC enforced server-side
- Responsive desktop-first UI

Architecture:

```text
Admin Browser
     ↓
Admin Frontend
     ↓
Authentication
     ↓
Backend API
     ↓
RBAC
     ↓
Domain Services
     ↓
Database
```

---

## 3. Admin Roles

Minimum roles:

### SUPER_ADMIN
Full system access.

### CONTENT_ADMIN
Content management only.

### SUPPORT_ADMIN
User/support operations.

### ANALYTICS_ADMIN
Analytics and reporting.

### FINANCE_ADMIN
Subscriptions and payment-related views.

### AI_ADMIN
AI configuration and insights.

Roles must be extensible.

---

## 4. RBAC

Permissions must be explicit.

Example:

```text
users.read
users.update
users.suspend

exercises.read
exercises.create
exercises.update
exercises.archive

workouts.read
workouts.create
workouts.publish

nutrition.read
nutrition.create
nutrition.publish

subscriptions.read
subscriptions.manage

notifications.create
notifications.send

analytics.read

settings.manage
```

Never implement:

```text
if role == "admin"
```

for complex authorization.

Use permission-based authorization.

---

## 5. Sections

Required sections:

- Dashboard
- Users
- Exercises
- Workout Plans
- Nutrition
- AI Insights
- Notifications
- Subscriptions
- Analytics
- Settings
- Audit Logs

---

## 6. Admin Dashboard

Metrics:

- Total users
- Active users
- New users
- Premium users
- Subscription revenue
- Workout completions
- Nutrition adherence
- AI usage
- Notification delivery
- Errors

Every metric must define:

- source
- calculation
- time range
- timezone
- caching policy

---

## 7. Users

Features:

- Search
- Filtering
- Pagination
- Sort
- User status
- Subscription status
- Last active
- Registration date
- View user
- Suspend
- Activate

Actions requiring confirmation:

- Suspend
- Delete
- Reset account state
- Revoke entitlement

Every destructive action must produce an audit event.

---

## 8. User Detail

Must display:

- Profile
- Goals
- Activity
- Workout history
- Nutrition history
- Subscription
- Entitlements
- Notifications
- AI usage
- Account status
- Audit history

Sensitive information must be minimized.

---

## 9. Exercises

Admin can:

- Create
- Edit
- Archive
- Restore
- Search
- Filter
- Tag
- Assign muscle groups
- Assign equipment
- Set difficulty
- Add instructions
- Add media
- Add safety notes

Exercise publishing requires validation.

---

## 10. Workout Plans

Admin can:

- Create
- Edit
- Duplicate
- Archive
- Publish
- Unpublish

Workout structure:

```text
Plan
 └── Week
      └── Day
           └── Workout
                └── Exercise
                     ├── Sets
                     ├── Reps
                     ├── Duration
                     ├── Rest
                     └── Notes
```

Published content should use versioning.

Do not silently mutate already-published plans where historical user records depend on previous content.

---

## 11. Nutrition

Admin can manage:

- Foods
- Meals
- Recipes
- Nutrition plans
- Calories
- Protein
- Carbohydrates
- Fats
- Meal timing
- Dietary preferences
- Restrictions
- Allergens

Nutrition validation is required before publishing.

---

## 12. AI Insights

Admin can inspect:

- AI request volume
- Model usage
- Error rate
- Latency
- Token usage
- Recommendation categories
- Safety flags
- Feedback

Admin must not expose private user conversations unnecessarily.

---

## 13. Notifications

Admin can:

- Create notification
- Schedule
- Target audience
- Preview
- Send
- Cancel scheduled notification
- View delivery status

Targeting must support:

- All users
- Premium
- Free
- Inactive
- Specific locale
- User segment

Avoid arbitrary SQL-like targeting from UI.

---

## 14. Subscriptions

Admin can:

- Search
- Filter
- View subscription
- View status
- View entitlement
- View provider events
- View payment lifecycle

Financial mutations must be restricted to appropriate permissions.

---

## 15. Analytics

Analytics must support:

- Date range
- Aggregation
- Export if authorized
- Pagination for large datasets

Never load millions of rows directly into the browser.

Use backend aggregation.

---

## 16. Admin UX Requirements

Every async operation must support:

- Loading
- Success
- Error
- Empty
- Retry

Tables must support:

- Pagination
- Sorting
- Filtering
- Search
- Empty state

Destructive actions require:

```text
Action
↓
Confirmation
↓
Reason where applicable
↓
Mutation
↓
Success feedback
↓
Audit
```

---

## 17. Security

Admin authentication must be stronger than normal user authentication.

Requirements:

- Secure session
- MFA-ready architecture
- Short-lived access tokens/session
- Role enforcement
- Permission enforcement
- Audit logging
- Rate limiting
- CSRF protection where applicable
- Secure cookies where applicable
- No authorization based only on frontend state

---

## 18. Audit

Every sensitive admin action must record:

- actor
- action
- resource
- resource_id
- timestamp
- IP metadata where policy allows
- before state where appropriate
- after state
- reason
- request correlation ID

---

## 19. Content Publishing

Use lifecycle:

```text
DRAFT
↓
VALIDATION
↓
REVIEW
↓
PUBLISHED
↓
ARCHIVED
```

Where applicable.

Production user-facing content must never be directly created through database manipulation.

---

## 20. Testing

Test:

- RBAC
- Permission boundaries
- CRUD
- Pagination
- Filters
- Search
- Publishing
- Versioning
- Confirmation dialogs
- Audit events
- Error handling

---

## 21. Definition of Done

M12 is complete when:

- All admin sections exist
- Authentication works
- RBAC works server-side
- Permissions work
- CRUD is functional
- Search works
- Filters work
- Pagination works
- Empty/loading/error states exist
- Confirmation dialogs exist
- Audit logging exists
- Content publishing works
- No mock data remains
- Admin has automated tests
