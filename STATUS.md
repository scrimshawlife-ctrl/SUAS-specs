# STATUS.md — SUAS specification status (v0.1.0)

**SPEC-001 status:** `READY_FOR_REVIEW` (not `accepted`; not `released`; see [SPEC-001.md](SPEC-001.md))  
**Related:** [README.md](README.md), [ROADMAP.md](ROADMAP.md), [VERSIONING.md](VERSIONING.md), [TESTING.md](TESTING.md), [DECISIONS.md](DECISIONS.md), [MVP_REFERENCE.md](MVP_REFERENCE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md), [COMPLIANCE.md](COMPLIANCE.md), [APIS.md](APIS.md), [ONBOARDING.md](ONBOARDING.md), [SPEC-001.md](SPEC-001.md), [FRICTION.md](FRICTION.md)

---

## 1. Identity

| Field | Value |
|---|---|
| Product | Shut Up and Serve |
| System | SUAS |
| Specification repository | `scrimshawlife-ctrl/SUAS-specs` |
| Implementation repository | `scrimshawlife-ctrl/SUAS` |
| Specification version | `0.1.0` |
| Specification lifecycle | `draft` |
| SPEC-001 | `READY_FOR_REVIEW` (not `accepted`) |
| Phase | `SPECIFICATION_BOOTSTRAP` |
| Implementation authority | `NOT_YET_RELEASED` |
| Pilot readiness | `NOT_READY` |
| Production readiness | `NOT_READY` |

`SUAS-specs` is canonical. `SUAS` must conform to released specs. Draft specifications are not implementation authority.

---

## 2. Phase definition

`SPECIFICATION_BOOTSTRAP` means:

- the v0.1 stack exists as draft specification artifacts;
- no artifact is yet `accepted` or `released`;
- implementation has no released-spec contract to ship against;
- pilot/production operation is not authorized;
- production-hardening concerns are now explicitly specified before first release: MVP visual conformance, provider-neutral service fulfillment, scaling, and resilience.

Next governance step remains owner review. Agents must not self-accept.

---

## 3. Readiness gates

The system is not ready until the applicable testable gates pass in [TESTING.md](TESTING.md).

| Gate | Meaning | Status |
|---|---|---|
| **AUTH** | Veteran passwordless auth; responder/admin MFA; session invalidation/rate limits | `NOT_READY` |
| **CONSENT** | First-class Consent Grants; use-time evaluation; revocation stops future use | `NOT_READY` |
| **CHECK-IN** | Versioned questionnaire and deterministic signal separation | `NOT_READY` |
| **COORDINATION** | Case/Service Request state machines and responder actions conform | `NOT_READY` |
| **EXTERNAL_FULFILLMENT** | Provider-neutral capability ports, conforming adapters, manual fallback, idempotency/reconciliation | `NOT_READY` |
| **UI_CONFORMANCE** | Production visual/interaction surfaces remain aligned with referenced MVP and accessibility contract | `NOT_READY` |
| **SAFETY** | Red-state behavior; human review; no automated emergency dispatch or diagnosis claim | `NOT_READY` |
| **PRIVACY** | Data minimization, access logging, provider disclosure projection, no prod data in non-prod | `NOT_READY` |
| **SCALE** | Stateless horizontal app tier, durable jobs, bounded APIs, load/backpressure/tenant-fairness evidence | `NOT_READY` |
| **RESILIENCE** | Dependency failure, duplicate delivery, retry, reconciliation, dead-letter, restore drills pass | `NOT_READY` |
| **OPERATIONS** | Coverage, queue review, resource/provider health, follow-up, incidents/recovery are staffed/operable | `NOT_READY` |
| **REPORTING** | Allowed operational metrics can be produced; forbidden clinical metrics absent | `NOT_READY` |

Overall MVP gate: `NOT_READY`.  
Overall production gate: `NOT_READY`.

The controlled pilot may use the same production-grade architecture with a smaller configured capacity target; the pilot size does not waive correctness, consent, provider-idempotency, UI-conformance, or durability requirements once running with real users.

---

## 4. Production-hardening artifacts

| Artifact | Status | Authority |
|---|---|---|
| [MVP_REFERENCE.md](MVP_REFERENCE.md) | `draft` | Visual/interaction conformance contract; referenced MVP is source of truth subject to canonical production overrides |
| [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md) | `draft` | Vendor-neutral service fulfillment adapters/ports |
| [SCALING.md](SCALING.md) | `draft` | Capacity bands, statelessness, durable work, bounded APIs, load testing |
| [RESILIENCE.md](RESILIENCE.md) | `draft` | Timeouts, retries, unknown outcomes, backpressure, failure drills, restore |

These artifacts do not select vendors. They define seams that must survive provider and infrastructure choices.

---

## 5. Artifact version status

| Artifact | Current version | Lifecycle |
|---|---|---|
| Specification stack | `0.1.0` | `draft` |
| MVP visual reference contract | `0.1.0` | `draft` |
| Provider integration contract | `0.1.0` | `draft` |
| Scale contract | `0.1.0` | `draft` |
| Resilience contract | `0.1.0` | `draft` |
| Questionnaire | `NOT_COMPUTABLE` (no published QuestionnaireVersion) | — |
| Support-signal rules | D-011 `DECISION_PENDING` | — |
| Event schema | `0.1.0` specified, not released | `draft` |
| API | `0.1.0` specified, not released | `draft` |
| Consent template | `NOT_COMPUTABLE` | — |

---

## 6. Established product/architecture decisions

- Product identity, mission, canonical loop, roles, and non-goals remain unchanged.
- MVP service categories remain `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.
- Referenced MVP visual hierarchy and interaction model are to be preserved under [MVP_REFERENCE.md](MVP_REFERENCE.md), with explicit production divergences for auth/safety/privacy/accessibility/domain correctness.
- Modular monolith remains default; microservices are not the default scaling answer.
- Production application tier is designed for stateless horizontal scaling.
- Production-critical asynchronous work must be durable; exact mechanism is D-022.
- External infrastructure/service APIs are capability ports; vendor SDKs remain adapter-local.
- Transportation, temporary shelter/rooms, food, and peer support use provider-neutral ports; manual coordination is first-class.
- Specific fulfillment providers remain D-017–D-020 `DECISION_PENDING`.
- Provider-specific status does not redefine canonical Service Request/Fulfillment state.
- External mutations require Fulfillment Attempt idempotency/reconciliation semantics.
- Capacity bands in [SCALING.md](SCALING.md) are test envelopes, not forecasts.
- First-release scale target, performance SLOs, and RTO/RPO remain D-021/D-023/D-024.
- Consent, safety, AI, Medi-Cal/billing, pilot geography/size, and compliance boundaries remain as previously specified.

---

## 7. Open decisions

See [DECISIONS.md](DECISIONS.md). Key new production decisions include:

- D-017 transportation provider adapter(s);
- D-018 temporary shelter/room provider adapter(s);
- D-019 food-support provider adapter(s);
- D-020 external peer-support adapter if any;
- D-021 first-release capacity band;
- D-022 durable job/queue implementation;
- D-023 performance SLOs/alerts;
- D-024 RTO/RPO.

Do not guess values or vendors.

---

## 8. Implementation handoff

Implementation authority remains `NOT_YET_RELEASED` until the required roadmap artifacts are owner-accepted and a first released specification cut is made.

The existence of an implementation repo or existing MVP does not authorize shipping against draft contracts.
