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

SPEC-001 does **not** accept later contracts for consent, safety, domain/data/API behavior, MVP visual conformance, provider integrations, scaling, resilience, testing, deployment, pilot operation, or release.

---

## 2. Later-spec boundaries

| Artifact / area | Roadmap authority |
|---|---|
| Consent/privacy/safety/security | SPEC-002 |
| Check-In / Support Signal | SPEC-003 |
| Cases / Service Requests / responder workflow | SPEC-004 |
| Resources / fulfillment / follow-up / settlement | SPEC-005 |
| Domain/data/event models | SPEC-006 |
| Core architecture / API / auth / notifications / admin | SPEC-007 |
| [MVP_REFERENCE.md](MVP_REFERENCE.md) | SPEC-008 |
| [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md) | SPEC-009 |
| [SCALING.md](SCALING.md) | SPEC-010 |
| [RESILIENCE.md](RESILIENCE.md) | SPEC-011 |
| Testing/readiness gates | SPEC-012 |
| Deployment/operations/recovery | SPEC-013 |
| Pilot/analytics | SPEC-014 |
| Release-blocking decisions | SPEC-015 |
| First released specification cut | SPEC-016 |
| Implementation conformance | SPEC-017 |
| Pilot/production readiness | SPEC-018 |
| Post-launch revision | SPEC-019 |

Accepting SPEC-001 does not accept any later artifact.

---

## 3. Owner checklist

Every item starts unchecked. Only `@scrimshawlife-ctrl` may mark it complete.

### README.md

- [ ] Mission and product boundaries match [PRODUCT.md](PRODUCT.md).
- [ ] Roles and canonical loop are correct.
- [ ] Cross-repo authority is explicit: `SUAS-specs` is canonical; `SUAS` implements released specs.
- [ ] Pilot remains approximately 25–50 veterans in Santa Clara County and is `NOT_READY`.
- [ ] Production-hardening artifacts are indexed without being represented as accepted.

### PRODUCT.md

- [ ] Mission wording is accepted.
- [ ] Role set is accepted; Organization Administrator ≠ SUAS System Administrator.
- [ ] MVP categories remain `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.
- [ ] No EHR, diagnosis, automated emergency dispatch, VA-integration, or MVP billing claim is introduced.
- [ ] D-011 and D-012 remain open unless separately decided.

### GLOSSARY.md

- [ ] Canonical terms and forbidden aliases are correct.
- [ ] Compliance Register is defined as a register, not a compliance claim.

### AGENTS.md

- [ ] Agents cannot self-accept/self-release or redefine specs from implementation.
- [ ] Application code remains out of this repository.

### VERSIONING.md

- [ ] Lifecycle remains `draft` / `accepted` / `released` / `superseded`.
- [ ] Current stack remains `0.1.0` / `draft`.

### STATUS.md

- [ ] Phase remains `SPECIFICATION_BOOTSTRAP`.
- [ ] Implementation authority is `NOT_YET_RELEASED`.
- [ ] Pilot and production readiness are `NOT_READY`.
- [ ] All 12 readiness gates are `NOT_READY`: AUTH, CONSENT, CHECK-IN, COORDINATION, EXTERNAL_FULFILLMENT, UI_CONFORMANCE, SAFETY, PRIVACY, SCALE, RESILIENCE, OPERATIONS, REPORTING.
- [ ] SPEC-001 remains `READY_FOR_REVIEW`, not accepted.

### DECISIONS.md

- [ ] D-001 through D-024 are represented correctly.
- [ ] D-017–D-020 do not select actual service providers.
- [ ] D-021–D-024 do not invent capacity, queue technology, SLOs, RTO, or RPO.
- [ ] No partner, vendor, legal/compliance status, signal weight, or safety copy is invented.

### Cross-cutting

- [ ] Pilot scope is not confused with production architectural capacity.
- [ ] Provider-neutral architecture does not redefine canonical product states.
- [ ] Referenced MVP visual authority does not override safety, consent, privacy, authentication, accessibility, or canonical domain rules.

---

## 4. Terminology freeze

Canonical product terms include: SUAS, Veteran, Responder, Trusted Circle, Trusted Contact, Organization, Organization Administrator, SUAS System Administrator, Service Provider, Support Signal, Check-In, Support Case, Service Request, Referral, Resource, Fulfillment, Fulfillment Attempt, Follow-Up, Settlement, Consent Grant, Domain Event, Audit Event, Pilot, Assignment, Case Note, Contact Attempt, QuestionnaireVersion, Compliance Register, External API, Capability Port, First-run / Bootstrap Checklist.

Support Signal levels: `GREEN`, `YELLOW`, `ORANGE`, `RED`.

MVP service categories: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.

Forbidden contract aliases include `ticket` for Support Case, `alert` for Support Signal, Referral for Service Request, unsupported HIPAA-compliant claims, and vague product claims such as `AI-powered` or `smart matching` without exact behavior.

---

## 5. Open decisions

D-001 through D-024 remain governed by [DECISIONS.md](DECISIONS.md). SPEC-001 acceptance does not close them.

New production-hardening decisions include:

- D-017 transportation adapter(s)
- D-018 temporary shelter/room adapter(s)
- D-019 food-support adapter(s)
- D-020 peer-support adapter if any
- D-021 first-release capacity band
- D-022 durable job/queue implementation
- D-023 performance SLOs/alerts
- D-024 RTO/RPO

No value or vendor may be guessed.

---

## 6. Acceptance procedure

1. Owner completes the checklist.
2. Only the owner may promote the SPEC-001 authority files to `accepted` according to [VERSIONING.md](VERSIONING.md).
3. `accepted` is not `released`.
4. Implementation authority remains `NOT_YET_RELEASED` until **SPEC-016** creates the first released specification cut.
5. Later specs remain independently unaccepted unless their own roadmap stages are completed.

---

## 7. Exit criteria

SPEC-001 exits when:

- the seven authority files are owner-accepted for the current stack version;
- terminology/authority is frozen;
- no conflicting alias or authority statement remains.

**Implementation handoff:** none.

---

## 8. Non-goals

- implementation
- self-acceptance
- accepting SPEC-002 through SPEC-019
- closing D-001 through D-024
- selecting providers or infrastructure vendors
- inventing legal/compliance status, performance targets, capacity forecasts, signal rules, or safety copy
