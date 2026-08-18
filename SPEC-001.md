# SPEC-001.md — Owner review worksheet (SUAS v0.1)

**SPEC-001 status:** `READY_FOR_REVIEW` (not `accepted`; not `released`)  
**Stack version:** `0.1.0` / `draft`  
**Roadmap item:** [ROADMAP.md](ROADMAP.md) SPEC-001  
**Specification owner:** `@scrimshawlife-ctrl`  
**Related:** [README.md](README.md), [PRODUCT.md](PRODUCT.md), [GLOSSARY.md](GLOSSARY.md), [AGENTS.md](AGENTS.md), [VERSIONING.md](VERSIONING.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md), [CHANGELOG.md](CHANGELOG.md), [CONTRIBUTING.md](CONTRIBUTING.md)

This file is an owner review worksheet. It is not implementation authority. Agents must not tick items or set lifecycle to `accepted` or `released`.

---

## 1. Scope

SPEC-001 is the owner review of the v0.1 product and authority set:

- [README.md](README.md)
- [PRODUCT.md](PRODUCT.md)
- [GLOSSARY.md](GLOSSARY.md)
- [AGENTS.md](AGENTS.md)
- [VERSIONING.md](VERSIONING.md)
- [STATUS.md](STATUS.md)
- [DECISIONS.md](DECISIONS.md)

All remain `draft` until the owner accepts them.

SPEC-001 may establish governing principles that later stages must implement consistently, including MVP-reference preservation, provider neutrality/manual-provider viability, scalable seams without premature distributed complexity, and separation of provider behavior from canonical product state.

Accepting these principles does **not** accept later detailed contracts.

---

## 2. Later-spec boundaries

| Area | Roadmap authority |
|---|---|
| Consent/privacy/safety/security | SPEC-002 |
| Check-In / Support Signal | SPEC-003 |
| Cases / Service Requests / responder workflow | SPEC-004 |
| Resources / fulfillment / follow-up / settlement | SPEC-005 |
| Domain/data/event/architecture reconciliation | SPEC-006 |
| Core architecture / API / auth / notifications / admin | SPEC-007 |
| MVP visual/interaction conformance | SPEC-008 |
| Provider-neutral service fulfillment | SPEC-009 |
| Scaling | SPEC-010 |
| Resilience | SPEC-011 |
| Testing/readiness gates | SPEC-012 |
| Deployment/operations/recovery | SPEC-013 |
| Pilot/analytics | SPEC-014 |
| Release-blocking decisions | SPEC-015 |
| First released specification cut | SPEC-016 |
| Implementation conformance | SPEC-017 |
| Pilot/production readiness | SPEC-018 |
| Post-launch revision | SPEC-019 |

---

## 3. Owner checklist

Only `@scrimshawlife-ctrl` may mark these complete.

### README / PRODUCT
- [ ] Mission/product boundaries and canonical loop are correct.
- [ ] `SUAS-specs` is canonical; `SUAS` implements released specs.
- [ ] Pilot remains approximately 25–50 veterans in Santa Clara County and `NOT_READY`.
- [ ] MVP categories remain `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.
- [ ] No EHR/diagnosis/automated emergency dispatch/VA-integration/MVP billing claim is introduced.
- [ ] Service Providers do not require APIs; provider brands are not domain semantics.
- [ ] Pilot size is operating scope, not architecture ceiling.

### GLOSSARY
- [ ] Canonical terms/forbidden aliases are correct.
- [ ] `Fulfillment Attempt` and Provider Adapter/Capability Port terminology preserve provider neutrality.
- [ ] Compliance Register is not a compliance claim.

### AGENTS / VERSIONING
- [ ] Agents cannot self-accept/self-release or redefine specs from implementation.
- [ ] Lifecycle is `draft|accepted|released|superseded` and acceptance is stage-specific.
- [ ] First implementation-authoritative release remains SPEC-016.

### STATUS
- [ ] Phase remains `SPECIFICATION_BOOTSTRAP`.
- [ ] Implementation authority is `NOT_YET_RELEASED`; pilot/production readiness `NOT_READY`.
- [ ] All 12 readiness gates remain `NOT_READY`.
- [ ] SPEC-001 is `READY_FOR_REVIEW`, not accepted.

### DECISIONS
- [ ] D-001 through D-025 are represented correctly.
- [ ] D-017–D-020 do not select actual providers.
- [ ] D-021–D-024 do not invent capacity, queue technology, SLOs, or RTO/RPO.
- [ ] D-025 does not invent a small-cell/reporting privacy threshold.
- [ ] No partner/vendor/legal status/signal weight/safety copy/reporting threshold is invented.

### Cross-cutting
- [ ] Pilot scope is not confused with production capacity.
- [ ] Provider-neutral architecture does not redefine product states.
- [ ] MVP visual authority does not override safety/consent/privacy/auth/accessibility/domain rules.
- [ ] Governing principles do not bypass SPEC-002 through SPEC-015 detailed acceptance.

---

## 4. Terminology freeze

Canonical terms include SUAS, Veteran, Responder, Trusted Circle, Trusted Contact, Organization, Organization Administrator, SUAS System Administrator, Service Provider, Support Signal, Check-In, Support Case, Service Request, Referral, Resource, Fulfillment, Fulfillment Attempt, Follow-Up, Settlement, Consent Grant, Domain Event, Audit Event, Pilot, Assignment, Case Note, Contact Attempt, QuestionnaireVersion, Compliance Register, External API, Capability Port, Provider Adapter, First-run / Bootstrap Checklist.

Support Signal levels: `GREEN`, `YELLOW`, `ORANGE`, `RED`.  
MVP service categories: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.

Forbidden aliases/claims remain those defined in [GLOSSARY.md](GLOSSARY.md).

---

## 5. Open decisions

D-001 through D-025 remain governed by [DECISIONS.md](DECISIONS.md). SPEC-001 acceptance closes none of them.

Production-hardening decisions include provider adapters D-017–D-020, release workload D-021, durable jobs D-022, SLOs D-023, RTO/RPO D-024, and aggregate reporting privacy D-025.

No value/vendor/threshold may be guessed.

---

## 6. Acceptance procedure

1. Owner completes checklist.
2. Owner may promote only the SPEC-001 authority files to `accepted` according to VERSIONING.
3. `accepted` is not `released`.
4. Implementation authority remains `NOT_YET_RELEASED` until SPEC-016.
5. Later specs remain independently unaccepted until their stages complete.

---

## 7. Exit criteria

SPEC-001 exits when the seven authority files are owner-accepted, terminology/authority is frozen, and no conflicting alias/authority statement remains.

**Implementation handoff:** none.

---

## 8. Non-goals

Implementation; self-acceptance; accepting SPEC-002–SPEC-019; closing D-001–D-025; selecting vendors; inventing legal/compliance status, performance/capacity/recovery targets, signal/safety rules, or reporting-privacy thresholds.
