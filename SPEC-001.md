# SPEC-001.md — Owner review worksheet (SUAS v0.1)

**SPEC-001 status:** `READY_FOR_REVIEW` (not `accepted`; not `released`)  
**Stack version:** `0.1.0` / `draft`  
**Roadmap item:** [ROADMAP.md](ROADMAP.md) SPEC-001  
**Specification owner:** `@scrimshawlife-ctrl`  
**Related:** [README.md](README.md), [PRODUCT.md](PRODUCT.md), [GLOSSARY.md](GLOSSARY.md), [AGENTS.md](AGENTS.md), [VERSIONING.md](VERSIONING.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md), [CHANGELOG.md](CHANGELOG.md), [CONTRIBUTING.md](CONTRIBUTING.md)

This file is a **review worksheet**. It is not implementation. Completing it does not authorize `SUAS` to ship. Agents must not tick items or set lifecycle to `accepted`.

---

## 1. What SPEC-001 is

SPEC-001 is the owner review of the **v0.1 product and authority set**.

Objective (from [ROADMAP.md](ROADMAP.md)): review and accept [README.md](README.md), [PRODUCT.md](PRODUCT.md), [GLOSSARY.md](GLOSSARY.md), [AGENTS.md](AGENTS.md), [VERSIONING.md](VERSIONING.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md).

This worksheet makes that review tickable. Current lifecycle of those seven files remains `draft`. This worksheet status is `READY_FOR_REVIEW`.

---

## 2. What SPEC-001 is not

- Not implementation. Implementation handoff is **none**.
- Not SPEC-002 or later. Consent, safety, check-in, cases, dispatch, data, API, testing, deployment, pilot operations, and the first `released` cut are later specs.
- Not a self-acceptance. Agents must not set `accepted` or `released`.
- Not a claim that the stack is `released` or that the pilot is `READY`.
- Not a close of D-001 through D-016.

---

## 3. Files in scope

| File | Role in SPEC-001 |
|---|---|
| [README.md](README.md) | Product identity, loop, index, authority |
| [PRODUCT.md](PRODUCT.md) | Mission, roles, categories, loop, non-goals |
| [GLOSSARY.md](GLOSSARY.md) | Terminology authority |
| [AGENTS.md](AGENTS.md) | Agent rules; no self-accept |
| [VERSIONING.md](VERSIONING.md) | Semver and lifecycle |
| [STATUS.md](STATUS.md) | Phase, gates, SPEC-001 status |
| [DECISIONS.md](DECISIONS.md) | Open decisions; do not guess |

### 3.1 Out of SPEC-001 acceptance (already drafted)

These files exist and may be linked. They are **not** this acceptance. Do not treat them as `accepted` when SPEC-001 is accepted.

| File | Later spec / owner |
|---|---|
| [COMPLIANCE.md](COMPLIANCE.md) | SPEC-002 / counsel (D-013) |
| [APIS.md](APIS.md) | SPEC-007 |
| [ONBOARDING.md](ONBOARDING.md) | First-run; not SPEC-001 acceptance |
| [API.md](API.md), [AUTH.md](AUTH.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [ADMIN.md](ADMIN.md) | SPEC-007 |
| [CONSENT.md](CONSENT.md), [PRIVACY.md](PRIVACY.md), [SAFETY.md](SAFETY.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [SECURITY.md](SECURITY.md) | SPEC-002 |
| Domain, data, event, case, dispatch, and remaining files | SPEC-003 through SPEC-015 |

Same-day gap closures in this pass (contact-log commands, veteran visibility default, enrollment self-attest default, notification attempt shape) are **specified** in those later files so implementation does not invent them. Specifying a gap is not SPEC-001 acceptance of those files.

---

## 4. Review checklist

The specification owner (`@scrimshawlife-ctrl`) must tick each item. Every item starts **UNCHECKED**. Status of this worksheet: `READY_FOR_REVIEW` (not `accepted`).

Use `[x]` only when the owner has actually reviewed the item. Agents must leave these unchecked.

### 4.1 README.md

- [ ] UNCHECKED — Mission and "what SUAS is / is not" match [PRODUCT.md](PRODUCT.md).
- [ ] UNCHECKED — Roles match [GLOSSARY.md](GLOSSARY.md) and [PRODUCT.md](PRODUCT.md).
- [ ] UNCHECKED — Canonical loop is SIGNAL → NEED → CONSENT → COORDINATION → FULFILLMENT → FOLLOW-UP → SETTLEMENT.
- [ ] UNCHECKED — Non-interchangeable concepts are listed and not aliased.
- [ ] UNCHECKED — Cross-repo authority: `SUAS-specs` canonical; `SUAS` implementation; draft is not implementation authority.
- [ ] UNCHECKED — Pilot: approximately 25–50 veterans, Santa Clara County; partners are placeholders; readiness `NOT_READY`.
- [ ] UNCHECKED — No HIPAA/compliance claim; no automated emergency dispatch; no marketing fluff.

### 4.2 PRODUCT.md

- [ ] UNCHECKED — Mission wording is the product contract.
- [ ] UNCHECKED — Role set is complete and Organization Administrator is not SUAS System Administrator.
- [ ] UNCHECKED — MVP categories are only `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.
- [ ] UNCHECKED — Non-goals include no EHR, no diagnosis, no automated dispatch, no VA-integrated benefits, no Medi-Cal billing in MVP.
- [ ] UNCHECKED — Medi-Cal / billing path is `FUTURE`.
- [ ] UNCHECKED — D-011 and D-012 remain open (signal weights, safety copy). Do not invent values.

### 4.3 GLOSSARY.md

- [ ] UNCHECKED — Every canonical term in section 5 of this worksheet is defined and used exactly.
- [ ] UNCHECKED — Forbidden aliases in section 5 do not appear as contract names.
- [ ] UNCHECKED — Compliance Register is defined as not a compliance claim.

### 4.4 AGENTS.md

- [ ] UNCHECKED — Ten required rules are intact.
- [ ] UNCHECKED — Agents must not clone implementation, must not write application code here, must not self-accept or self-release.

### 4.5 VERSIONING.md

- [ ] UNCHECKED — Lifecycle states are `draft` / `accepted` / `released` / `superseded`.
- [ ] UNCHECKED — Current stack is `0.1.0` / `draft`.
- [ ] UNCHECKED — Only the owner may move `draft` to `accepted` or `accepted` to `released`.

### 4.6 STATUS.md

- [ ] UNCHECKED — Phase is `SPECIFICATION_BOOTSTRAP`.
- [ ] UNCHECKED — Implementation authority is `NOT_YET_RELEASED`. Pilot readiness is `NOT_READY`.
- [ ] UNCHECKED — All eight MVP gates are `NOT_READY`.
- [ ] UNCHECKED — SPEC-001 is `READY_FOR_REVIEW`, not `accepted`.

### 4.7 DECISIONS.md

- [ ] UNCHECKED — D-001 through D-016 are listed and open.
- [ ] UNCHECKED — No partner, vendor, HIPAA status, signal weight, or safety copy has been invented.
- [ ] UNCHECKED — "Already decided" list has not been silently expanded.

### 4.8 Cross-cutting

- [ ] UNCHECKED — Terminology freeze (section 5) holds across the seven files.
- [ ] UNCHECKED — Alias scan (section 6) has no remaining contract aliases.
- [ ] UNCHECKED — Residual issues (section 9) stay open.

---

## 5. Terminology freeze

Canonical terms are defined in [GLOSSARY.md](GLOSSARY.md). Use them exactly in UI copy, API resources, and database names that leak into contracts.

### 5.1 Canonical terms (must use)

SUAS, Veteran, Responder, Trusted Circle, Trusted Contact, Organization, Organization Administrator, SUAS System Administrator, Service Provider, Support Signal, Check-In, Support Case, Service Request, Referral, Resource, Fulfillment, Follow-Up, Settlement, Consent Grant, Domain Event, Audit Event, Pilot, Assignment, Case Note, Contact Attempt, QuestionnaireVersion, Compliance Register, External API, Capability Port, First-run / Bootstrap Checklist.

Support Signal levels: `GREEN`, `YELLOW`, `ORANGE`, `RED`.

MVP service categories: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.

### 5.2 Forbidden aliases (must not use as contract names)

| Forbidden | Use instead |
|---|---|
| ticket | Support Case |
| alert (as the signal) | Support Signal |
| referral (as the request) | Service Request |
| case (as the request) | Service Request |
| HIPAA-compliant / HIPAA compliant | `HIPAA_APPLICABILITY = DECISION_PENDING` |
| AI-powered | (omit; specify exact behavior) |
| smart matching | (omit; specify exact behavior) |
| seamless | (omit) |
| intelligent | (omit) |
| automatically handles | (omit; specify exact behavior) |

"Trusted-contact alert" is a **named notify action**, not an alias of Support Signal.

Non-interchangeable (from [PRODUCT.md](PRODUCT.md) section 6): Check-In is not Support Signal is not Support Case is not Service Request is not Referral is not Assignment is not Fulfillment is not Follow-Up is not Settlement. Follow-Up is not a Case Note. Case Note is not a Contact Attempt. Settlement is not a clinical outcome. Assignment is not Fulfillment.

---

## 6. Alias scan results (this pass)

Searched the seven SPEC-001 files for: `ticket`, alert-as-signal, referral-as-request, case-as-request, `HIPAA-compliant`, `HIPAA compliant`, `AI-powered`, `smart matching`, `seamless`, `intelligent`, `automatically handles`.

| File | Hits | Disposition |
|---|---|
| [README.md](README.md) | "trusted-contact alert" in the loop table; "AI-powered" in the quality-rules prohibition list | Not a contract alias. No repair. |
| [PRODUCT.md](PRODUCT.md) | "trusted-contact alert" as a notify action; "ticket-without-history" in the Support Case "Is not" table | Not a contract alias. No repair. |
| [GLOSSARY.md](GLOSSARY.md) | "alerts require a matching Consent Grant"; "does not make SUAS HIPAA-compliant" (prohibition) | Not a contract alias. No repair. |
| [AGENTS.md](AGENTS.md) | Forbids aliasing Support Case to `ticket`; forbids "AI-powered" | Prohibition. No repair. |
| [VERSIONING.md](VERSIONING.md) | None | — |
| [STATUS.md](STATUS.md) | None | — |
| [DECISIONS.md](DECISIONS.md) | None | — |

**Remaining contract aliases in the seven files: none. Repaired in this pass: none.**

A forbidden-alias list was added to [GLOSSARY.md](GLOSSARY.md) so the freeze lives in the terminology authority, not only in this worksheet.

---

## 7. How acceptance happens

1. The owner completes every checklist item in section 4.
2. Only the specification owner (`@scrimshawlife-ctrl`) may then set lifecycle to `accepted` in **all three** of:
   - [STATUS.md](STATUS.md)
   - [VERSIONING.md](VERSIONING.md)
   - [CHANGELOG.md](CHANGELOG.md)
3. Agents must not self-accept. Agents must not set `accepted` or `released` on any spec.
4. `accepted` is not `released`. Implementation authority stays `NOT_YET_RELEASED` until SPEC-012.
5. Ticking this worksheet without the three-file lifecycle edit is not acceptance.

See [VERSIONING.md](VERSIONING.md) section 3.1 and [AGENTS.md](AGENTS.md).

---

## 8. Exit criteria (from ROADMAP SPEC-001)

Copied from [ROADMAP.md](ROADMAP.md) SPEC-001. Not met until the owner accepts.

- Those files marked `accepted` for stack `0.1.0`
- Terminology frozen
- No remaining aliasing of non-interchangeable concepts

**Implementation handoff:** none. Implementation must not start on product identity alone.

**Current state vs exit criteria:** the seven files are `READY_FOR_REVIEW` / `draft`. Exit criteria are **not** met.

---

## 9. Residual issues that stay open

From [DECISIONS.md](DECISIONS.md). Do not guess. Do not close in this pass.

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
| D-013 | Counsel review of COMPLIANCE.md register before pilot |
| D-014 | Whether a geocoding/maps API is required for MVP |
| D-015 | Case-note veteran visibility (MVP default `INFERRED`: veterans cannot see full Case Notes) |
| D-016 | Enrollment identity-proofing beyond self-attest (MVP default `INFERRED`: self-attest + working email and/or phone) |

D-015 and D-016 record `INFERRED` MVP operational defaults so implementation does not invent a clinical chart or a VA partnership. The decisions remain open.

---

## 10. Same-day gap closures (not SPEC-001 acceptance)

Specified in this pass so later specs are not blocked by "do not invent." These files are **not** accepted by SPEC-001.

| Gap | Where specified | Decision still open? |
|---|---|---|
| Contact log commands | [API.md](API.md), [APIS.md](APIS.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [EVENT_MODEL.md](EVENT_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md) | Path gap closed. |
| Case Note veteran visibility | [CASES.md](CASES.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [PRODUCT.md](PRODUCT.md), [PRIVACY.md](PRIVACY.md) | D-015 remains open. |
| Enrollment identity-proofing | [ONBOARDING.md](ONBOARDING.md), [PILOT.md](PILOT.md), [AUTH.md](AUTH.md), [PRODUCT.md](PRODUCT.md) | D-016 remains open. |
| Notification attempt shape | [NOTIFICATIONS.md](NOTIFICATIONS.md), [DATA_MODEL.md](DATA_MODEL.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md) | Shape specified. Vendors D-003/D-004 still open. |

---

## 11. Non-goals of this worksheet

- Implementation
- Self-acceptance
- Closing D-001 through D-016
- Marking any spec `accepted` or `released`
- Inventing partners, vendors, HIPAA status, signal weights, or safety copy
