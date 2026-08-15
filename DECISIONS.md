# DECISIONS.md — Open decisions (do not guess)

**SPEC-001 status:** `READY_FOR_REVIEW` (not `accepted`; not `released`; see [SPEC-001.md](SPEC-001.md))  
**Related:** [STATUS.md](STATUS.md), [PRODUCT.md](PRODUCT.md), [ARCHITECTURE.md](ARCHITECTURE.md), [SECURITY.md](SECURITY.md), [PRIVACY.md](PRIVACY.md), [COMPLIANCE.md](COMPLIANCE.md), [APIS.md](APIS.md), [ONBOARDING.md](ONBOARDING.md), [PILOT.md](PILOT.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [SAFETY.md](SAFETY.md), [SPEC-001.md](SPEC-001.md), [FRICTION.md](FRICTION.md)

This file lists decisions that are **not** made in v0.1.0. Implementation must not invent values. Use `DECISION_PENDING` or `NOT_COMPUTABLE` until a later spec version records a decision.

---

## 1. Rules

1. Do not guess.
2. Do not treat a vendor preference, a blog post, or an informal conversation as a decision.
3. Do not lock a cloud provider, auth vendor, SMS vendor, or email vendor in architecture or deployment.
4. Do not name pilot partner organizations. Use `PARTNER_ORG_001` placeholders.
5. Do not write exact Support Signal scoring weights until they are specified, versioned, and test-vectored.
6. When a decision is made, record: decision, date (PT), owner, spec version, supersedes.

---

## 2. Open decisions

| ID | Decision | Status | Blocking |
|---|---|---|---|
| D-001 | Production hosting / cloud provider | `DECISION_PENDING` | Production [DEPLOYMENT.md](DEPLOYMENT.md) |
| D-002 | Auth provider (or in-house) | `DECISION_PENDING` | [AUTH.md](AUTH.md) implementation |
| D-003 | SMS provider | `DECISION_PENDING` | SMS in [NOTIFICATIONS.md](NOTIFICATIONS.md) |
| D-004 | Email provider | `DECISION_PENDING` | Email in [NOTIFICATIONS.md](NOTIFICATIONS.md) |
| D-005 | Database hosting | `DECISION_PENDING` | Production [DEPLOYMENT.md](DEPLOYMENT.md) |
| D-006 | Legal / HIPAA classification | `DECISION_PENDING` (`HIPAA_APPLICABILITY = DECISION_PENDING`) | Legal claims; do not claim compliance |
| D-007 | Retention durations (per entity and backups) | `DECISION_PENDING` | [PRIVACY.md](PRIVACY.md), [SECURITY.md](SECURITY.md) |
| D-008 | Pilot partner organizations | `DECISION_PENDING` (use `PARTNER_ORG_001` …) | [PILOT.md](PILOT.md) named staffing |
| D-009 | Responder staffing model and coverage hours | `DECISION_PENDING` | [OPERATIONS.md](OPERATIONS.md), [PILOT.md](PILOT.md) |
| D-010 | Service funding sources and amounts | `DECISION_PENDING` / funding path is `FUTURE` | [SETTLEMENT.md](SETTLEMENT.md) billing |
| D-011 | Exact Support Signal scoring rules and thresholds | `DECISION_PENDING` | [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md) release |
| D-012 | Approved safety copy (crisis-resource text shown in red-state) | `DECISION_PENDING` | [SAFETY.md](SAFETY.md) release |
| D-013 | Counsel review of [COMPLIANCE.md](COMPLIANCE.md) regime register before operating the 25–50 veteran pilot | `DECISION_PENDING` | [PILOT.md](PILOT.md) operation; not a compliance claim |
| D-014 | Whether a geocoding / maps API is required for MVP (`coverage_geometry` vs county list) | `DECISION_PENDING` | [RESOURCES.md](RESOURCES.md), [APIS.md](APIS.md) `GEOCODE_MAPS` |
| D-015 | Case-note veteran visibility (whether veterans may see full Case Notes later) | `DECISION_PENDING` (MVP default `INFERRED`: veterans cannot see full Case Notes; not a clinical chart) | [CASES.md](CASES.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [PRODUCT.md](PRODUCT.md) |
| D-016 | Enrollment identity-proofing beyond self-attest | `DECISION_PENDING` (MVP default `INFERRED`: self-attested veteran status + working email and/or phone via passwordless auth; no VA identity API, no DD-214 upload, no in-person proofing for the 25–50 Santa Clara pilot). Do not invent a VA partnership. | [ONBOARDING.md](ONBOARDING.md), [PILOT.md](PILOT.md), [AUTH.md](AUTH.md) |

---

## 2.1 Proposed closes (not accepted)

Analysis in [FRICTION.md](FRICTION.md) proposes two owner closes. **Neither is accepted in this file.** D-012 and D-014 remain `DECISION_PENDING` in the table above. Agents must not treat the proposals as decisions.

| ID | Proposal (see [FRICTION.md](FRICTION.md)) | Status in this file |
|---|---|---|
| D-014 | `GEOCODE_MAPS` not required for MVP | Still `DECISION_PENDING` |
| D-012 (narrow) | 911 + 988 display-only; SUAS does not call; local copy remains open | Still `DECISION_PENDING`; remainder (local copy) stays open even if the narrow close is later accepted |

---

## 3. What is already decided (do not reopen without a spec change)

- Product is coordination, not diagnosis, not EHR, not automated emergency dispatch.
- Canonical loop and non-interchangeable concepts.
- Modular monolith for the pilot.
- Passwordless veteran auth (magic link, email OTP, phone OTP where supported). No passwords unless later justified.
- No social login assumption.
- MFA required for Responders and Administrators.
- No generative model for the primary Support Signal.
- No generative AI for safety-critical decisions.
- No automated 911 / emergency dispatch.
- Medi-Cal / billing adapter is `FUTURE`. Do not assert activities are Medi-Cal billable.
- Pilot geography: Santa Clara County, California. Size: approximately 25–50 veterans.
- Specs are canonical; implementation cites.

---

## 4. Decision record template (for future versions)

```
### D-0XX — <title>
- Status: decided
- Date (PT):
- Owner:
- Spec version:
- Decision:
- Alternatives considered:
- Consequences:
- Supersedes:
```
