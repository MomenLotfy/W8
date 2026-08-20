# W8 Master Technical Specification

**Product:** W8  
**Document:** W8 Master Technical Specification  
**Status:** Authoritative  
**Purpose:** Central index for the complete W8 technical specification and implementation documentation.

---

## 1. Specification

The W8 technical specification is divided into the following documents for easier navigation and AI-assisted implementation.

> These documents are not separate specifications. Together, they constitute the complete W8 Master Technical Specification.

| Sections | Specification Document |
|---|---|
| 01 | [W8_SPEC_01.md](specification/01_W8_FOUNDATION.md) |
| 02–05 | [W8_SPEC_02_05.md](specification/02_W8_FOUNDATION.md) |
| 06–10 | [W8_SPEC_06_10.md](specification/03_W8_FOUNDATION.md) |
| 11–15 | [W8_SPEC_11_15.md](specification/04_W8_FOUNDATION.md) |
| 16–20 | [W8_SPEC_16_20.md](specification/05_W8_FOUNDATION.md) |
| 21–25 | [W8_SPEC_21_25.md](specification/06_W8_FOUNDATION.md) |
| 26–29 | [W8_SPEC_26_29.md](specification/07_W8_FOUNDATION.md) |

---

## 2. Design & Prototype References

The following files are design and audit references only.

They MUST NOT override the technical specification or implementation rules.

### Approved UI/UX Prototype

[Prototype — index.html](design/Index.html)

### Prototype Audit Report

[Prototype Audit Report](design/W8_PROTOTYPE_AUDIT_REPORT.md)
The prototype is a visual and interaction reference.

It MUST NOT be treated as production architecture or copied directly into the Flutter application.

---

## 3. Implementation Milestones

Implementation is organized into sequential engineering milestones.

All milestone documents are located under:

`docs/implementation/`

| Milestone | Document |
|---|---|
| M0 | [M0_ENGINEERING_FOUNDATION.md](implementation/M0_ENGINEERING_FOUNDATION.md) |
| M1 | [M1_BACKEND_FOUNDATION.md](implementation/M1_BACKEND_FOUNDATION.md) |
| M2 | [M2_IDENTITY.md](implementation/M2_IDENTITY.md) |
| M3 | [M3_CORE_USER_DOMAIN.md](implementation/M3_CORE_USER_DOMAIN.md) |
| M4 | [M4_CONTENT_MANAGEMENT.md](implementation/M4_CONTENT_MANAGEMENT.md) |
| M5 | [M5_WORKOUT_ENGINE.md](implementation/M5_WORKOUT_ENGINE.md) |
| M6 | [M6_NUTRITION_ENGINE.md](implementation/M6_NUTRITION_ENGINE.md) |
| M7 | [M7_MOBILE_CORE_UI.md](implementation/M7_MOBILE_CORE_UI.md) |
| M8 | [M8_AI_COACH.md](implementation/M8_AI_COACH.md) |
| M9 | [M9_PERSONALIZATION.md](implementation/M9_PERSONALIZATION.md) |
| M10 | [M10_NOTIFICATIONS.md](implementation/M10_NOTIFICATIONS.md) |
| M11 | [M11_PAYMENTS.md](implementation/M11_PAYMENTS.md) |
| M12 | [M12_ADMIN_DASHBOARD.md](implementation/M12_ADMIN_DASHBOARD.md) |
| M13 | [M13_OBSERVABILITY.md](implementation/M13_OBSERVABILITY.md) |
| M14 | [M14_SECURITY_HARDENING.md](implementation/M14_SECURITY_HARDENING.md) |
| M15 | [M15_TESTING_QA.md](implementation/M15_TESTING_QA.md) |
| M16 | [M16_STAGING.md](implementation/M16_STAGING.md) |
| M17 | [M17_PRODUCTION_READINESS.md](implementation/M17_PRODUCTION_READINESS.md) |
| M18 | [M18_PRODUCTION_RELEASE.md](implementation/M18_PRODUCTION_RELEASE.md) |
| M19 | [M19_POST_LAUNCH.md](implementation/M19_POST_LAUNCH.md) |

---

## 4. Authority Hierarchy

When implementing W8, the following authority order MUST be respected:

1. `AGENTS.md`
2. Relevant W8 specification section(s)
3. Relevant implementation milestone
4. Approved prototype and audit documentation
5. Existing implementation

If a conflict is discovered, the AI agent MUST NOT invent a resolution.

It MUST report the conflict before proceeding.

---

## 5. AI Implementation Model

The W8 project MUST be implemented incrementally.

The AI coding agent MUST NOT attempt to build the entire application in a single operation.

The required execution model is:

```text
Specification
      ↓
Milestone
      ↓
Task
      ↓
Implementation Plan
      ↓
Code
      ↓
Format
      ↓
Static Analysis
      ↓
Tests
      ↓
Security Checks
      ↓
Diff Review
      ↓
Acceptance Criteria
      ↓
Commit / Pull Request
      ↓
Next Task
