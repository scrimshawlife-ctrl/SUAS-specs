# STATUS.md — SUAS specification status (v0.1.0)

**Specification lifecycle:** `draft`  
**Phase:** `SPECIFICATION_BOOTSTRAP`  
**Implementation authority:** `NOT_YET_RELEASED`  
**Pilot readiness:** `NOT_READY`  
**Production readiness:** `NOT_READY`

**Related:** [README.md](README.md), [ROADMAP.md](ROADMAP.md), [VERSIONING.md](VERSIONING.md), [TESTING.md](TESTING.md), [DECISIONS.md](DECISIONS.md), [MVP_REFERENCE.md](MVP_REFERENCE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md)

---

## 1. Governance frontier

| Stage | Status | Worksheet |
|---|---|---|
| SPEC-001 | `READY_FOR_REVIEW` | [SPEC-001.md](SPEC-001.md) |
| SPEC-002 | `BLOCKED_BY_SPEC_001`; preflight complete | [SPEC-002.md](SPEC-002.md) |
| SPEC-003 | `BLOCKED_BY_SPEC_001_AND_SPEC_002`; preflight complete | [SPEC-003.md](SPEC-003.md) |
| SPEC-004 | `BLOCKED_BY_SPEC_001_AND_SPEC_002`; preflight complete | [SPEC-004.md](SPEC-004.md) |
| SPEC-005 | `BLOCKED_BY_SPEC_004`; preflight complete | [SPEC-005.md](SPEC-005.md) |
| SPEC-006+ | not owner-preflighted as dedicated worksheets unless otherwise noted in roadmap | [ROADMAP.md](ROADMAP.md) |

Preflight means draft contradictions were repaired and a review worksheet may exist. It does **not** mean the stage is accepted.

Agents must not tick owner checkboxes or change lifecycle.

---

## 2. Identity

| Field | Value |
|---|---|
| Product | Shut Up and Serve |
| System | SUAS |
| Specification repository | `scrimshawlife-ctrl/SUAS-specs` |
| Implementation repository | `scrimshawlife-ctrl/SUAS` |
| Specification version | `0.1.0` |
| Specification lifecycle | `draft` |
| Phase | `SPECIFICATION_BOOTSTRAP` |
| Implementation authority | `NOT_YET_RELEASED` |
| Pilot readiness | `NOT_READY` |
| Production readiness | `NOT_READY` |

`SUAS-specs` is canonical. `SUAS` must conform to released specs. Draft/preflight/accepted artifacts are not implementation authority until the release stage says otherwise.

---

## 3. Current production-hardening conclusions

Draft governing principles now include:

- referenced MVP visual/interaction preservation with explicit production overrides;
- provider-neutral transportation/shelter/food/peer-support capability ports;
- manual coordination as first-class fulfillment path;
- stateless horizontal application tier;
- durable production-critical asynchronous work;
- command/job/provider idempotency separated from immutable event identity;
- atomic one-winner semantics for contested Case/Request commands;
- minimum-necessary provider disclosure with use-time consent/basis;
- Resource catalog freshness separated from live provider availability;
- Follow-Up business retries separated from notification/job retries;
- repeated Case resolution requires durable multi-cycle Settlement history;
- provider callbacks are evidence, not canonical state authority.

These principles remain draft until their roadmap stages are owner-accepted/released.

---

## 4. Readiness gates

All remain `NOT_READY`:

| Gate | Meaning | Status |
|---|---|---|
| **AUTH** | Veteran passwordless auth; responder/admin MFA; session invalidation/rate limits | `NOT_READY` |
| **CONSENT** | first-class use-time Consent Grants / documented basis | `NOT_READY` |
| **CHECK-IN** | versioned questionnaire + deterministic replay-safe Support Signal separation | `NOT_READY` |
| **COORDINATION** | Case/Request state machines, atomic claims/assignments, responder actions | `NOT_READY` |
| **EXTERNAL_FULFILLMENT** | provider-neutral adapters/manual fallback/idempotency/reconciliation | `NOT_READY` |
| **UI_CONFORMANCE** | referenced MVP visual/interaction + accessibility conformance | `NOT_READY` |
| **SAFETY** | red-state behavior; no automated dispatch/diagnosis | `NOT_READY` |
| **PRIVACY** | minimization/access logging/provider projection/no prod data in non-prod | `NOT_READY` |
| **SCALE** | stateless app tier/durable jobs/bounded APIs/load/backpressure/tenant fairness | `NOT_READY` |
| **RESILIENCE** | dependency failure/retry/reconciliation/dead-letter/restore drills | `NOT_READY` |
| **OPERATIONS** | coverage/resource/provider/follow-up/incident/recovery operations | `NOT_READY` |
| **REPORTING** | allowed operational metrics; forbidden clinical metrics absent | `NOT_READY` |

Overall MVP gate: `NOT_READY`.  
Overall production gate: `NOT_READY`.

---

## 5. Key unresolved decisions

See [DECISIONS.md](DECISIONS.md). Important unresolved production decisions include:

- D-001–D-005 hosting/auth/SMS/email/database providers;
- D-006 legal/HIPAA classification;
- D-007 retention;
- D-008/D-009 pilot partners/staffing;
- D-011 Support Signal scoring;
- D-012 approved safety copy;
- D-013 counsel review;
- D-014 geocoding/maps necessity;
- D-015/D-016 case-note visibility / identity proofing beyond MVP defaults;
- D-017–D-020 service-provider adapter enablement;
- D-021 first-release capacity band;
- D-022 durable job/queue implementation;
- D-023 performance SLOs/alerts;
- D-024 RTO/RPO.

No value/vendor is guessed.

---

## 6. Required SPEC-006 reconciliation queue

Preflight of SPEC-003–005 has produced explicit schema/model obligations for SPEC-006:

1. deterministic Support Signal computation identity and effective-signal projection;
2. event identity/idempotency/correlation/causation fields and replay-safe event publication;
3. one-active-case/active-assignment concurrency representation;
4. Service Request current assignment/provider history representation;
5. first-class `Settlement` entity/table or equivalent durable multi-cycle resolution history;
6. deterministic current/latest Settlement projection;
7. blocking vs carried-forward Follow-Up representation;
8. Follow-Up schedule/version identity for stale-job invalidation;
9. FulfillmentAttempt/current ServiceFulfillment/history constraints;
10. command idempotency persistence/lookup where API-only representation is insufficient.

These are not implementation choices to be made silently.

---

## 7. Artifact status

| Artifact | Current version | Lifecycle |
|---|---|---|
| Specification stack | `0.1.0` | `draft` |
| MVP visual reference contract | `0.1.0` | `draft` |
| Provider integration contract | `0.1.0` | `draft` |
| Scale contract | `0.1.0` | `draft` |
| Resilience contract | `0.1.0` | `draft` |
| API | `0.1.0` specified, not released | `draft` |
| Event schema | `0.1.0` draft, under SPEC-003/006 reconciliation | `draft` |
| Questionnaire content | `NOT_COMPUTABLE` | — |
| Support Signal production rules | D-011 `DECISION_PENDING` | — |
| Consent template | `NOT_COMPUTABLE` | — |

---

## 8. Implementation handoff

Implementation authority remains `NOT_YET_RELEASED` until the required roadmap stages are owner-accepted and SPEC-016 creates a first released specification cut.

The existence of an implementation repository, prototype, MVP, provider integration, queue worker, or production deployment does not redefine this authority.
