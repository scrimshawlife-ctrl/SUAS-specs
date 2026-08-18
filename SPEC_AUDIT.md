# SPEC_AUDIT.md — v0.1.0 bootstrap and production-readiness preflight audit

**Original bootstrap:** 2026-08-14 PT  
**Production-readiness preflight:** 2026-08-18 PT  
**Stack:** `0.1.0` / `draft`  
**Phase:** `SPECIFICATION_BOOTSTRAP`  
**Authority:** audit only; not acceptance, release, legal/security certification, or readiness claim.

---

## 1. Current verdict

`PRODUCTION_SPEC_PREFLIGHT_COMPLETE_THROUGH_SPEC_016_ASSEMBLY`

The draft stack now has a preflighted path from governing product authority through first-release assembly. It remains:

- `draft`;
- `NOT_YET_RELEASED`;
- pilot `NOT_READY`;
- production `NOT_READY`.

No agent has accepted/released a stage or selected production vendors.

---

## 2. Product canon preserved

The hardening pass preserves:

1. SUAS as a consent-governed veteran support coordination platform.
2. Mission: shortest safe and consented path from current need to available human/material support.
3. `SIGNAL → NEED → CONSENT → COORDINATION → FULFILLMENT → FOLLOW-UP → SETTLEMENT`.
4. MVP categories `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.
5. Assignment ≠ Fulfillment; Referral ≠ Request; Follow-Up ≠ Note; Settlement ≠ clinical outcome.
6. No EHR/diagnosis/suicide prediction/automated emergency dispatch/MVP billing claim.
7. Deterministic non-generative primary Support Signal.
8. First-class use-time Consent.
9. Controlled pilot approximately 25–50 veterans in Santa Clara County unless explicitly changed.
10. Compliance register is not a compliance claim; D-006/D-013 govern legal posture/review.

---

## 3. Production-hardening canon drafted

The preflight now explicitly defines:

- live referenced MVP as visual/interaction authority with safe/truthful production divergences;
- provider-neutral `TransportationPort`, `TemporaryShelterPort`, `FoodSupportPort`, `PeerSupportPort`;
- Manual Adapter/manual coordination as first-class;
- stateless horizontally scalable app tier;
- durable production-critical async work;
- persistent API command idempotency distinct from event identity;
- replay-safe Domain Event publication/outbox-equivalent semantics;
- shared single-use auth challenge/session revoke semantics across instances;
- atomic contested Case/assignment/Settlement operations;
- Support Signal computation identity and deterministic effective projection;
- first-class `FulfillmentAttempt` and `PROVIDER_UNKNOWN` reconciliation;
- first-class multi-cycle Settlement history/current projection;
- Follow-Up schedule identity/stale-job suppression/blocking vs carry-forward;
- Notification logical-send dedupe distinct from transport/business retry;
- provider callback/auth/privacy/normalization boundaries;
- bounded/paginated production access paths;
- scale targets as release-specific D-021/D-023 evidence, not invented forecasts;
- resilience for internal replay/staleness plus external dependency failure;
- controlled pilot cohort separated from traction/waitlist/technical capacity;
- operational analytics separated from clinical/causal claims;
- D-025 aggregate reporting privacy policy as an explicit open decision.

---

## 4. Roadmap / worksheets

Canonical roadmap: SPEC-001 through SPEC-019.

Current preflight frontier:

- SPEC-001 `READY_FOR_REVIEW`;
- SPEC-002 through SPEC-015 dependency-blocked with dedicated owner worksheets/preflight complete;
- SPEC-016 blocked release-cut checklist staged;
- SPEC-017/018 remain post-release implementation/readiness stages;
- SPEC-019 future revision.

First implementation-authoritative release remains SPEC-016.

Preflight does not satisfy dependencies.

---

## 5. Readiness gates

Current gates:

`AUTH`, `CONSENT`, `CHECK-IN`, `COORDINATION`, `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SAFETY`, `PRIVACY`, `SCALE`, `RESILIENCE`, `OPERATIONS`, `REPORTING`.

All are `NOT_READY`.

[TESTING.md](TESTING.md) now requires evidence for cross-instance auth revoke, command replay/conflict, signal dedupe/event recovery, Case concurrency, Settlement cycles, stale Follow-Up jobs, Notification logical-send dedupe, provider conformance, truthful QRF degraded states, load/backpressure, failure drills, and restore.

---

## 6. Open decisions

D-001 through D-025 are canonical open decisions in [DECISIONS.md](DECISIONS.md).

Production-hardening set:

| ID | Topic |
|---|---|
| D-017 | transportation external adapter(s) |
| D-018 | temporary shelter/room external adapter(s) |
| D-019 | food-support external adapter(s) |
| D-020 | external peer-support adapter if any |
| D-021 | release-specific workload/capacity envelope |
| D-022 | durable job/queue implementation |
| D-023 | release performance SLOs/alerts |
| D-024 | release RTO/RPO/restore objectives |
| D-025 | aggregate reporting privacy/small-cell aggregation/suppression policy |

Unsupported numeric regional/multi-region capacity figures and the earlier `<5` reporting suppression value were removed rather than promoted to canon.

---

## 7. Major contradictions found and repaired

1. No explicit visual source of truth → `MVP_REFERENCE.md`.
2. Auth/email/SMS capability ports existed but rides/rooms/food/peer integration did not → `PROVIDER_INTEGRATIONS.md` + domain reconciliation.
3. Pilot-scale architecture assumptions → stateless/durable scale contract.
4. External failure semantics incomplete → resilience/degradation contract.
5. Old 8-gate model → 12-gate readiness model.
6. Old SPEC-001–015 roadmap/release numbering → SPEC-001–019; first release SPEC-016.
7. SPEC-001/STATUS/VERSIONING/AGENTS governance drift → stage-specific acceptance/release rules.
8. Fulfillment Attempt frozen in worksheet but missing terminology → GLOSSARY reconciliation.
9. Provider calls missing from consent/privacy/security disclosure model → explicit provider disclosure boundary.
10. Signal job replay could duplicate settlement → stable computation identity/idempotent settlement.
11. `event_id` overloaded as idempotency → distinct event/idempotency/correlation/causation/request identity.
12. Case creation/claim/assignment races → atomic one-winner semantics.
13. Invalid generic escalation could imply unassigned `OPEN/TRIAGED → ACTIVE` → repaired transition contract.
14. Resource freshness conflated with live availability → separated.
15. Follow-Up retry count mixed business and transport/job retry → separated.
16. Settlement required but no durable multi-cycle entity → first-class Settlement model + resolution cycles.
17. API version selection delegated to implementation → `/api/v0` canonical selector fixed.
18. Auth session/revoke correctness not explicitly horizontal → shared authoritative challenge/session semantics.
19. Notification duplicate generating work lacked logical-send identity → dedupe contract added.
20. Scaling included unsupported numeric growth bands → removed; D-021 owns release envelope.
21. Resilience focused mostly on external providers → expanded to command/event/session/stale-job/restore failures.
22. Incident taxonomy omitted queue/idempotency/event/provider/QRF truth failures → expanded.
23. Fulfillment analytics mixed `COMPLETED` and `CONFIRMED` → separated.
24. Reporting privacy used invented `<5` threshold → removed; D-025 opened.
25. Traction could be misread as pilot expansion → controlled cohort/waitlist/capacity boundaries clarified.

---

## 8. Remaining production risks are now explicit, not resolved

- vendor/provider/infrastructure decisions remain open;
- legal classification/counsel review/retention remain open;
- Support Signal production thresholds and approved safety copy remain open;
- actual partners/staffing remain open;
- release workload/SLO/recovery objectives remain open;
- reporting privacy threshold remains open;
- no implementation conformance evidence exists yet;
- no readiness-gate evidence exists yet.

The spec architecture is substantially more production-oriented, but these unresolved items prevent a production-ready claim.

---

## 9. Release path

1. Owner reviews/accepts stages in ROADMAP order.
2. SPEC-015 produces D-001–D-025 release decision ledger: `DECIDED` or safe `DEFERRED_FOR_RELEASE`.
3. SPEC-016 creates a pinned release manifest and owner changes named artifacts to `released`.
4. Only that released cut becomes `RELEASED_FOR_IMPLEMENTATION`.
5. SPEC-017 compares the implementation with released contracts.
6. SPEC-018 requires actual test/load/failure/restore/operations evidence for launch.
7. SPEC-019 incorporates measured post-launch findings.

---

## 10. Production-code confirmation

This repository remains specification authority only. This audit does not authorize production code, deployment, real veteran data, provider credentials, or unreviewed implementation defaults.
