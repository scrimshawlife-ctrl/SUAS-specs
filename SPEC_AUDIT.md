# SPEC_AUDIT.md — v0.1.0 bootstrap audit

**Date:** 2026-08-14 (PT)  
**Stack version:** `0.1.0` / `draft`  
**Phase:** `SPECIFICATION_BOOTSTRAP`  
**Auditor role:** specification bootstrap (this file is not a legal or security certification)

**Related:** [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md), [ROADMAP.md](ROADMAP.md), [README.md](README.md), [COMPLIANCE.md](COMPLIANCE.md), [APIS.md](APIS.md), [ONBOARDING.md](ONBOARDING.md), [FRICTION.md](FRICTION.md)

---

## 1. Files created

Markdown and hygiene only. No application or production code. No git clone.

### Product and authority
- [README.md](README.md)
- [PRODUCT.md](PRODUCT.md)
- [GLOSSARY.md](GLOSSARY.md)
- [STATUS.md](STATUS.md)
- [VERSIONING.md](VERSIONING.md)
- [ROADMAP.md](ROADMAP.md)
- [DECISIONS.md](DECISIONS.md)
- [AGENTS.md](AGENTS.md)
- [CONTRIBUTING.md](CONTRIBUTING.md)
- [CHANGELOG.md](CHANGELOG.md)
- [SPEC-001.md](SPEC-001.md)
- [FRICTION.md](FRICTION.md)
- [CODEOWNERS](CODEOWNERS)
- [.gitignore](.gitignore)

### Architecture and data
- [ARCHITECTURE.md](ARCHITECTURE.md)
- [DOMAIN_MODEL.md](DOMAIN_MODEL.md)
- [DATA_MODEL.md](DATA_MODEL.md)
- [EVENT_MODEL.md](EVENT_MODEL.md)
- [API.md](API.md)
- [APIS.md](APIS.md)

### Domain
- [AUTH.md](AUTH.md)
- [CONSENT.md](CONSENT.md)
- [CHECKINS.md](CHECKINS.md)
- [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md)
- [SAFETY.md](SAFETY.md)
- [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md)
- [CASES.md](CASES.md)
- [DISPATCH.md](DISPATCH.md)
- [RESOURCES.md](RESOURCES.md)
- [REFERRALS.md](REFERRALS.md)
- [FULFILLMENT.md](FULFILLMENT.md)
- [FOLLOWUP.md](FOLLOWUP.md)
- [SETTLEMENT.md](SETTLEMENT.md)
- [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md)
- [NOTIFICATIONS.md](NOTIFICATIONS.md)

### Security, privacy, operations
- [ADMIN.md](ADMIN.md)
- [SECURITY.md](SECURITY.md)
- [PRIVACY.md](PRIVACY.md)
- [COMPLIANCE.md](COMPLIANCE.md)
- [ONBOARDING.md](ONBOARDING.md)
- [PILOT.md](PILOT.md)
- [ANALYTICS.md](ANALYTICS.md)
- [TESTING.md](TESTING.md)
- [DEPLOYMENT.md](DEPLOYMENT.md)
- [OPERATIONS.md](OPERATIONS.md)
- [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md)

### This audit
- [SPEC_AUDIT.md](SPEC_AUDIT.md)

---

## 2. Canonical decisions established

These are specified and must not be reopened without a spec change:

1. Product identity: Shut Up and Serve / SUAS is a consent-governed veteran support coordination platform.
2. Mission: shortest safe and consented path from current need to available human or material support.
3. Non-goals: not a crisis-prediction app, not an EHR, not a diagnosis tool, not an automated emergency-dispatch platform.
4. Canonical loop: SIGNAL → NEED → CONSENT → COORDINATION → FULFILLMENT → FOLLOW-UP → SETTLEMENT.
5. Non-interchangeable concepts: Check-In, Support Signal, Support Case, Service Request, Referral, Assignment, Fulfillment, Follow-Up, Settlement.
6. Support Case = coordination around a veteran; Service Request = a specific requested need; one case may contain many requests.
7. Roles: Veteran, Responder, Organization Administrator, Trusted Contact, Service Provider, SUAS System Administrator. Org-admin ≠ SUAS-admin.
8. MVP categories: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`. Future reserved: `BENEFITS`, `HOUSING`, `HEALTHCARE_NAVIGATION`, `COMMUNITY`, `OTHER`.
9. Support Case states: `OPEN`, `TRIAGED`, `ASSIGNED`, `ACTIVE`, `FOLLOWUP`, `RESOLVED`, `CLOSED`.
10. Service Request states: `CREATED`, `SUBMITTED`, `TRIAGED`, `MATCHING`, `ASSIGNED`, `ACCEPTED`, `IN_PROGRESS`, `FULFILLED`, `CONFIRMED`, `CLOSED` plus `CANCELLED`, `DECLINED`, `EXPIRED`, `UNFULFILLABLE`, `ESCALATED`.
11. Support Signal levels: `GREEN`, `YELLOW`, `ORANGE`, `RED`. Deterministic; no generative primary signal; no silent historical mutation.
12. Consent is first-class grants, not a boolean. Revocation stops future use. Historical audit preserved separately.
13. Trusted Circle membership grants no visibility by itself.
14. Assignment is not Fulfillment. Referral send is not service received. Follow-Up is not a Case Note. Settlement is not a clinical outcome.
15. Architecture: modular monolith for the pilot. No cloud vendor locked.
16. Veteran auth: passwordless (magic link, email OTP, phone OTP where supported). No social-login assumption. MFA for responder/admin.
17. Notifications MVP: `EMAIL`, `SMS`, `IN_APP`. `PUSH` is `FUTURE`. Events/policies generate notifications; templates have no safety-critical logic.
18. AI policy: no generative AI for safety-critical decisions. Future assistive uses listed and bounded.
19. Medi-Cal / billing adapter: `FUTURE`. Path: Fulfillment → Funding Eligibility → Funding Source → Optional Billing Adapter. Do not assert billability.
20. `HIPAA_APPLICABILITY = DECISION_PENDING`. Do not claim compliance.
21. Pilot: ~25–50 veterans, Santa Clara County, California. Partners are placeholders `PARTNER_ORG_001` ….
22. Analytics: operational metrics only. Forbidden: suicides-prevented, clinical efficacy, suicide-prediction accuracy.
23. Authority: `SUAS-specs` canonical; `SUAS` implementation must cite released specs. Implementation authority `NOT_YET_RELEASED`. Pilot readiness `NOT_READY`.
24. Versioning: semver starting `0.1.0`; lifecycle `draft` / `accepted` / `released` / `superseded`.
25. Roadmap: SPEC-001 through SPEC-015.
26. MVP gates: AUTH, CONSENT, CHECK-IN, COORDINATION, SAFETY, PRIVACY, OPERATIONS, REPORTING — all `NOT_READY`.
27. Epistemic labels: `OBSERVED`, `INFERRED`, `SPECULATIVE`, `NOT_COMPUTABLE`, `DECISION_PENDING`, `FUTURE`.
28. Compliance is a **register**, not a claim. [COMPLIANCE.md](COMPLIANCE.md) does not make SUAS HIPAA-compliant, CCPA-compliant, TCPA-compliant, or anything-compliant. Counsel + D-006 own legal classification. D-013 (counsel review of the register) is required before pilot operation.
29. External APIs are **capability ports** (`SmsPort`, `EmailPort`, `AuthPort`, plus `RELATIONAL_DB` / `APP_HOST`). Vendors D-001–D-005 remain open. D-014 (geocoding/maps) is open. No 911 / VA / Medi-Cal clients in MVP. See [APIS.md](APIS.md).
30. First-run is a gated, persisted, auditable bootstrap. Empty responder queue is valid. No PRODUCTION demo-case seeding. See [ONBOARDING.md](ONBOARDING.md).

---

## 3. Open decisions (do not guess)

From [DECISIONS.md](DECISIONS.md):

| ID | Topic |
|---|---|
| D-001 | Production hosting / cloud provider |
| D-002 | Auth provider |
| D-003 | SMS provider |
| D-004 | Email provider |
| D-005 | Database hosting |
| D-006 | Legal / HIPAA classification |
| D-007 | Retention durations |
| D-008 | Pilot partner organizations |
| D-009 | Responder staffing / coverage hours |
| D-010 | Service funding |
| D-011 | Exact Support Signal scoring rules |
| D-012 | Approved safety copy |
| D-013 | Counsel review of COMPLIANCE.md regime register before pilot |
| D-014 | Whether a geocoding/maps API is required for MVP |
| D-015 | Case-note veteran visibility (MVP `INFERRED`: veterans cannot see full Case Notes) |
| D-016 | Enrollment identity-proofing beyond self-attest (MVP `INFERRED`: self-attest + working email and/or phone) |

---

## 4. Inconsistencies found and repaired

During bootstrap writing and inspection:

1. **State-machine names** were aligned across [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [API.md](API.md), [TESTING.md](TESTING.md), [GLOSSARY.md](GLOSSARY.md). No residual alias (no `ticket`, no `alert` as a signal).
2. **Event catalog** in [EVENT_MODEL.md](EVENT_MODEL.md) matches the names used in domain specs (22 types).
3. **Responder actions** in [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md) match the requested set of 11.
4. **Marketing terms** ("AI-powered", "smart matching", "seamless", "intelligent", "automatically handles") appear only in prohibition lists ([README.md](README.md), [AGENTS.md](AGENTS.md), [DISPATCH.md](DISPATCH.md), [ARCHITECTURE.md](ARCHITECTURE.md)), not as product claims.
5. **HIPAA** appears only as `DECISION_PENDING` / do-not-claim, never as a compliance assertion.
6. **One-active-case-per-veteran** was labeled `INFERRED` operational default in [CASES.md](CASES.md), not presented as a closed product law.
7. **Recommended timing constants** (OTP TTL 15 minutes, follow-up retries 3, notification retries 3, cell suppression <5) were labeled `INFERRED` and are not closed decisions.
8. Thinner first drafts of [ADMIN.md](ADMIN.md), [PRIVACY.md](PRIVACY.md), and [RESOURCES.md](RESOURCES.md) were expanded with events, actors, and links so they are not stubs.

No remaining internal contradiction was found in state names, event names, role names, or repo authority wording.

**SPEC-001 pass (same day, still `0.1.0` / `draft`):**

9. Contact-log paths were unspecified ("do not invent paths"). Specified as `POST /cases/{id}/commands/log-contact-attempt` and `POST /cases/{id}/commands/complete-contact`. Case Note is not a substitute.
10. Notification attempt shape was "implementation may pick." Specified: one Notification row; retries append immutable Audit Events; no child table.
11. Case Note veteran visibility and enrollment identity-proofing were `NOT_COMPUTABLE`. Recorded as `INFERRED` MVP defaults; opened D-015 and D-016 without closing them.
12. Alias scan of the seven SPEC-001 files found no remaining contract aliases. Forbidden-alias list added to [GLOSSARY.md](GLOSSARY.md).

**Friction analysis pass (same day, still `0.1.0` / `draft`):**

13. Added [FRICTION.md](FRICTION.md). Names user and infra friction and three proposed paths. Does not close D-001–D-016. Does not authorize implementation. Proposed D-012 / D-014 closes remain unaccepted. No partners, vendors, HIPAA status, signal weights, or local crisis-resource lists invented.

---

## 5. Known risks

| Risk | Why | Mitigation in this stack |
|---|---|---|
| Draft treated as shippable | Implementation repo exists | `NOT_YET_RELEASED`; [AGENTS.md](AGENTS.md) |
| Invented partners or VA/Medi-Cal facts | Pressure to look complete | Placeholders; `NOT_COMPUTABLE`; D-008/D-010 |
| Invented signal weights | Engine work starts early | D-011; unreleased fixtures only |
| Invented safety copy | Red-state UI needs words | D-012; placeholder slot |
| Consent bypass in a "small pilot" | Informal ops | [PILOT.md](PILOT.md) forbids bypass |
| Assignment treated as fulfillment | Common ops shortcut | Repeated in DISPATCH/FULFILLMENT/TESTING |
| Generative model used for signal or closure | Convenience | SAFETY + ARCHITECTURE + AGENTS |
| Cloud vendor locked in "just to deploy" | Infra momentum | D-001/D-005; DEPLOYMENT forbids |
| Audit purge | Storage pressure | Events immutable; D-007 open |
| Cross-tenant leakage | Shared monolith DB | tenant_id + critical suite |
| Stale resources used silently | Catalog rot | Freshness bands + ops duty |
| Legal overclaim | HIPAA/billing language | DECISION_PENDING / FUTURE |

---

## 6. Remaining NOT_COMPUTABLE items

Do not invent values for:

- Named partner organizations and their actual capabilities
- County agreements
- VA integrations
- Medi-Cal eligibility or billability
- Responder coverage hours and headcount
- Legal classification (HIPAA applicability)
- Counsel review of the compliance register (D-013)
- Whether a geocoding/maps API is required (D-014)
- Retention durations
- Published QuestionnaireVersion content
- Exact signal scoring rules and thresholds
- Approved safety copy
- Identity-proofing **beyond** the MVP self-attest default (D-016 remains open; MVP rule is specified as `INFERRED`)
- Whether veterans may later see full Case Notes (D-015 remains open; MVP default is specified as `INFERRED`: cannot see)
- Exact OTP/session/idle/expiry TTLs (only `INFERRED` recommendations exist)
- Coverage geometry storage type
- MFA factor product
- Dual-control break-glass steps for SUAS-admin recovery
- Pilot start/end calendar dates
- Export package format
- (removed) Notification-attempt row shape — now specified: one Notification row + immutable Audit Events per attempt ([NOTIFICATIONS.md](NOTIFICATIONS.md))

---

## 7. Recommended next run

**Owner review of [SPEC-001.md](SPEC-001.md).** SPEC-001 is `READY_FOR_REVIEW`, not `accepted`. Not implementation.

Do not start `SUAS` feature work against this `draft` stack. Do not clone the implementation repository as a substitute for accepting specs. Agents must not self-accept.

Suggested review order:

1. [SPEC-001.md](SPEC-001.md) checklist — owner ticks; agents leave unchecked
2. [README.md](README.md), [PRODUCT.md](PRODUCT.md), [GLOSSARY.md](GLOSSARY.md), [AGENTS.md](AGENTS.md)
3. [DECISIONS.md](DECISIONS.md) — confirm D-001 through D-016 remain open
4. [CONSENT.md](CONSENT.md), [SAFETY.md](SAFETY.md), [PRIVACY.md](PRIVACY.md), [COMPLIANCE.md](COMPLIANCE.md), [ONBOARDING.md](ONBOARDING.md) (not SPEC-001 acceptance)
5. [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md) state machines (not SPEC-001 acceptance)
6. Only `@scrimshawlife-ctrl` may promote SPEC-001 files to `accepted` after the [SPEC-001.md](SPEC-001.md) checklist is complete

---

## 8. Production-code confirmation

This directory contains markdown specifications and hygiene files (`.gitignore`, `CODEOWNERS`) only. There is no application source, no infrastructure-as-code that selects a vendor, no seed data with real veteran information, and no cloned git repository.
