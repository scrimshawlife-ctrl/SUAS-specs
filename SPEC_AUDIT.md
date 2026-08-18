# SPEC_AUDIT.md — v0.1.0 bootstrap and production-hardening audit

**Original bootstrap date:** 2026-08-14 (PT)  
**Production-hardening audit:** 2026-08-18 (PT)  
**Stack version:** `0.1.0` / `draft`  
**Phase:** `SPECIFICATION_BOOTSTRAP`  
**Authority:** audit only; not acceptance, release, legal certification, or security certification.

**Related:** [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md), [ROADMAP.md](ROADMAP.md), [README.md](README.md), [MVP_REFERENCE.md](MVP_REFERENCE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md)

---

## 1. Current audit verdict

`PRODUCTION_SPEC_HARDENING_IN_PROGRESS`

The specification set is materially stronger than the initial bootstrap, but remains `draft`, `NOT_YET_RELEASED`, and `NOT_READY` for pilot or production operation.

No application code, vendor selection, or implementation authority is created by this audit.

---

## 2. Canonical product decisions preserved

The production-hardening pass does not change the core product:

1. SUAS is a consent-governed veteran support coordination platform.
2. Mission: coordinate the shortest safe and consented path between a veteran's current need and an available human or material support resource.
3. Canonical loop: `SIGNAL → NEED → CONSENT → COORDINATION → FULFILLMENT → FOLLOW-UP → SETTLEMENT`.
4. MVP service categories remain `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.
5. Assignment ≠ Fulfillment; Referral ≠ Service Request; Follow-Up ≠ Case Note; Settlement ≠ clinical outcome.
6. No EHR, diagnosis, suicide prediction, automated emergency dispatch, or MVP Medi-Cal billing claim.
7. Support Signal remains deterministic and non-generative.
8. Consent remains first-class and evaluated at use time.
9. Pilot remains approximately 25–50 veterans in Santa Clara County unless a later accepted spec changes it.
10. Compliance remains a register, not a claim; `HIPAA_APPLICABILITY = DECISION_PENDING`.

---

## 3. Production-hardening decisions added

The following are now explicit draft architecture/product constraints:

1. The referenced MVP is the visual/interaction reference under [MVP_REFERENCE.md](MVP_REFERENCE.md), subject to safety, consent, privacy, auth, accessibility, and canonical-domain overrides.
2. Production application compute is stateless for correctness-critical state and horizontally scalable.
3. Production-critical asynchronous work is durable; exact job/queue product remains D-022.
4. External service fulfillment uses provider-neutral capability ports.
5. MVP fulfillment capabilities include transportation, temporary shelter/room, food support, and peer support.
6. Vendor SDKs, payloads, and statuses remain inside adapters.
7. Manual coordination is a first-class provider integration mode.
8. External mutations use `FulfillmentAttempt` idempotency/reconciliation semantics.
9. Ambiguous provider outcomes are reconciled before potentially duplicating a fulfillment action.
10. Capacity bands are test envelopes, not adoption forecasts.
11. Microservices, sharding, multi-region active-active, and distributed infrastructure are not required without measured need and a later released change.
12. Scale and resilience are release gates, not deferred post-pilot cleanup.

---

## 4. Roadmap and readiness model

The canonical roadmap now runs **SPEC-001 through SPEC-019**.

The first released specification cut is **SPEC-016**.

Readiness gates are:

- `AUTH`
- `CONSENT`
- `CHECK-IN`
- `COORDINATION`
- `EXTERNAL_FULFILLMENT`
- `UI_CONFORMANCE`
- `SAFETY`
- `PRIVACY`
- `SCALE`
- `RESILIENCE`
- `OPERATIONS`
- `REPORTING`

All are currently `NOT_READY`.

---

## 5. Open decisions

D-001 through D-024 are governed by [DECISIONS.md](DECISIONS.md). Do not guess.

New production-hardening decisions:

| ID | Topic |
|---|---|
| D-017 | Transportation provider adapter(s) |
| D-018 | Temporary shelter/room provider adapter(s) |
| D-019 | Food-support provider adapter(s) |
| D-020 | Peer-support provider adapter, if any |
| D-021 | First-release capacity band |
| D-022 | Durable job/queue implementation |
| D-023 | Performance SLOs and alert thresholds |
| D-024 | RTO/RPO |

Provider selection must not alter Service Request, Fulfillment, or Case semantics.

---

## 6. Cross-spec issues found and repaired on 2026-08-18

1. **Visual authority gap:** added [MVP_REFERENCE.md](MVP_REFERENCE.md).
2. **Service-provider API gap:** added [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md) and reconciled APIS/ARCHITECTURE/FULFILLMENT/DOMAIN_MODEL/DATA_MODEL.
3. **Pilot-scale architecture ceiling:** added [SCALING.md](SCALING.md); production application tier is stateless and production-critical jobs are durable.
4. **Dependency-failure semantics:** added [RESILIENCE.md](RESILIENCE.md).
5. **Readiness gate gap:** added `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SCALE`, and `RESILIENCE`.
6. **Roadmap gap:** expanded roadmap through SPEC-019 before first release.
7. **Deployment/operations drift:** updated deployment and day-2 operations for durable work, provider health, queue health, restore, and failure drills.
8. **Governance drift found during PR #4 audit:** [SPEC-001.md] still referenced the old eight-gate/D-001–D-016/SPEC-012 release model. Reconciled to the current 12 gates, D-001–D-024, and SPEC-016 first release.
9. **Pilot milestone drift:** [PILOT.md] referenced old SPEC-014/SPEC-015 milestones. Reconciled to SPEC-018 readiness and SPEC-019 revision.

---

## 7. Remaining production-readiness risks

| Risk | Current control |
|---|---|
| Draft treated as shippable | `NOT_YET_RELEASED`; owner-controlled lifecycle |
| Visual redesign drifts from known MVP | MVP reference contract + UI conformance gate |
| Service vendor becomes domain dependency | provider ports + adapter isolation |
| Provider duplicate booking on retry | Fulfillment Attempt idempotency + reconciliation |
| Provider/API outage loses a need | Service Request persists; manual/reroute/degraded path |
| Notification/provider storm | durable queues, backpressure, rate limiting, retry bounds |
| Multiple responders claim same work | atomic contested-command requirement |
| Pilot assumptions constrain growth | scale envelopes + stateless horizontal app tier |
| Cross-tenant leakage at higher volume | tenant-scoped data model + isolation suites |
| Audit/event growth degrades primary workload | bounded access, indexing, retention/archive decision work |
| Legal/compliance overclaim | D-006/D-013; explicit do-not-claim rules |
| Unproven recovery | RESILIENCE gate + D-024 + restore/failure drills |

---

## 8. Remaining NOT_COMPUTABLE / DECISION_PENDING signal

Do not invent:

- named fulfillment providers or partner capabilities;
- cloud/auth/SMS/email/database/job vendors;
- first-release capacity target;
- performance SLOs/alert thresholds;
- RTO/RPO;
- exact Support Signal scoring thresholds;
- approved safety copy;
- legal/HIPAA classification;
- retention durations;
- responder coverage hours/headcount;
- pilot start/end dates;
- provider contractual coverage, funding, pricing, or SLAs.

---

## 9. Recommended governance sequence

1. Review PR #4 cross-spec coherence.
2. Owner reviews [SPEC-001.md](SPEC-001.md); agents leave checkboxes untouched.
3. Proceed through [ROADMAP.md](ROADMAP.md) in order.
4. Close or safely defer release-specific decisions at SPEC-015.
5. Create first released contract only at SPEC-016.
6. Perform implementation conformance at SPEC-017.
7. Require all launch-applicable readiness evidence at SPEC-018.

---

## 10. Production-code confirmation

This repository remains specification-only. No application code, real veteran data, provider credentials, or vendor-specific production implementation is authorized by this audit.
