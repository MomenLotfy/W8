# M9 — W8 Personalization & Recommendation Engine

**Document:** M9_PERSONALIZATION.md
**Product:** W8
**Milestone:** M9
**Status:** Ready for Implementation
**Priority:** P0
**Depends On:** M0–M8
**Blocks:** M10, M12, M15, M16

---

# 1. Purpose

M9 implements deterministic and controlled personalization for W8.

Personalization determines which approved W8 content is most appropriate
for a user based on:

- goals
- profile
- training availability
- workout history
- nutrition context
- preferences
- progression
- engagement
- explicit constraints

---

# 2. Critical Principle

Personalization MUST NOT mean:

```text
LLM invents a workout
LLM invents a diet
LLM invents an exercise
```

Instead:

```text
User Context
     ↓
Eligibility Rules
     ↓
Candidate Content
     ↓
Scoring
     ↓
Ranking
     ↓
Approved Recommendation
```

The system recommends from approved content.

---

# 3. Source of Truth

Follow:

- AGENTS.md
- Master Specification
- Sections 03, 04, 06, 07, 12, 13, 20
- M3
- M4
- M5
- M6
- M8

---

# 4. Personalization Inputs

Potential inputs:

- goal
- fitness level
- training days
- preferred time
- equipment
- exercise history
- workout completion
- progress
- nutrition preferences
- dietary restrictions
- subscription entitlement
- content availability

Only use data explicitly authorized by the specification.

---

# 5. Hard Constraints vs Soft Preferences

This distinction is mandatory.

## Hard Constraints

A candidate MUST be excluded when it violates a hard constraint.

Examples:

- unavailable equipment
- explicit allergy
- unsupported content
- age restriction if applicable
- subscription restriction
- content status

## Soft Preferences

Soft preferences influence ranking.

Examples:

- preferred training time
- preferred exercise type
- preferred meal type
- historical preference

Never allow a soft preference to override a safety constraint.

---

# 6. Recommendation Pipeline

```text
Load User Context
      ↓
Load Eligible Published Content
      ↓
Apply Hard Constraints
      ↓
Generate Candidates
      ↓
Score Candidates
      ↓
Apply Diversity Rules
      ↓
Rank
      ↓
Return Recommendations
```

---

# 7. Eligibility

A recommendation must satisfy:

- published
- active
- valid
- user eligible
- business constraints
- safety constraints

---

# 8. Scoring

Scoring must be deterministic and explainable.

Do not start with an opaque ML model unless explicitly required.

Example conceptual scoring:

```text
goal_match
+
difficulty_match
+
equipment_match
+
preference_match
+
recent_success
+
content_priority
-
repetition_penalty
```

Exact weights must be defined in the business specification.

AI MUST NOT invent weights at runtime.

---

# 9. Diversity

Prevent recommendation repetition.

Examples:

- avoid same exercise repeatedly
- avoid same workout repeatedly
- avoid identical meal recommendations repeatedly

Diversity rules must not violate hard constraints.

---

# 10. Cold Start

New users have limited history.

Use:

- profile
- goals
- onboarding
- explicit preferences
- approved default recommendations

Do not pretend to have behavioral history that does not exist.

---

# 11. Feedback

Where supported, collect recommendation feedback.

Examples:

- accepted
- rejected
- skipped
- completed
- replaced

Use feedback to improve future ranking.

---

# 12. Explainability

Where recommendations are surfaced to users, the application should be able to provide a simple reason.

Example:

```text
Recommended because it matches your training goal
and preferred equipment.
```

Do not expose internal scoring weights unless explicitly intended.

---

# 13. AI Boundary

AI may assist with:

- natural-language explanation
- conversational interpretation
- summarization
- ranking assistance where explicitly approved

AI MUST NOT bypass:

- eligibility
- safety
- published-content requirements
- RBAC
- subscription entitlements

---

# 14. Recommendation Actions

If the recommendation system suggests an action, the application must validate the action before execution.

The recommendation engine must not directly:

- modify account
- charge payment
- publish content
- modify CMS
- create arbitrary workouts
- change security settings

---

# 15. Versioning

Recommendations should record:

- algorithm version
- rules version
- content version
- timestamp

This enables debugging and reproducibility.

---

# 16. Experimentation

If A/B testing is introduced later:

- use feature flags
- record experiment assignment
- preserve safety constraints
- do not change critical safety behavior via uncontrolled experiments

---

# 17. Caching

Cache candidate content where appropriate.

Never cache user-specific recommendations in a shared cache without strict user isolation.

---

# 18. Performance

Personalization should avoid:

- querying every exercise individually
- loading entire workout history unnecessarily
- repeated calculations
- unbounded recommendation candidate sets

Use bounded candidate pools.

---

# 19. Security

Protect against:

- cross-user personalization
- data leakage
- manipulated preferences
- subscription bypass
- unsafe recommendation

---

# 20. Tests

## Rule Tests

- hard constraints
- soft preferences
- ranking
- diversity
- cold start

## Security

- cross-user data
- entitlement bypass
- unsafe candidate

## Regression

Every business rule gets regression tests.

---

# 21. Definition of Done

- [ ] Context resolver implemented
- [ ] Eligibility engine implemented
- [ ] Hard constraints implemented
- [ ] Soft preferences implemented
- [ ] Candidate generation implemented
- [ ] Ranking implemented
- [ ] Diversity implemented
- [ ] Cold-start strategy implemented
- [ ] Feedback implemented where required
- [ ] Versioning implemented
- [ ] Explainability implemented
- [ ] AI boundaries enforced
- [ ] Security tests pass
- [ ] Regression suite passes

---

# 22. AI Execution Prompt

Implement M9.

Do not begin with machine learning.
Start with deterministic rules.
Read Sections 03, 04, 12, 13 and all preceding milestones.
Never recommend unpublished content.
Never override hard safety constraints.
Never use another user's history.
Record algorithm/content versions.
Write tests for every eligibility rule before implementing ranking.
If a rule cannot be expressed clearly, STOP and request clarification.
