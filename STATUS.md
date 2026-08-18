# STATUS.md — SUAS specification status (v0.1.0)

**Specification lifecycle:** `draft`  
**Phase:** `SPECIFICATION_BOOTSTRAP`  
**Implementation authority:** `NOT_YET_RELEASED`  
**Pilot readiness:** `NOT_READY`  
**Production readiness:** `NOT_READY`

**Related:** [README.md](README.md), [ROADMAP.md](ROADMAP.md), [VERSIONING.md](VERSIONING.md), [TESTING.md](TESTING.md), [DECISIONS.md](DECISIONS.md), [MVP_REFERENCE.md](MVP_REFERENCE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md)

---

## 1. Governance frontier

| Stage | Status |
|---|---|
| SPEC-001 | `READY_FOR_REVIEW` |
| SPEC-002 | dependency-blocked; preflight complete |
| SPEC-003 | dependency-blocked; preflight complete |
| SPEC-004 | dependency-blocked; preflight complete |
| SPEC-005 | dependency-blocked; preflight complete |
| SPEC-006 | dependency-blocked; preflight complete |
| SPEC-007 | dependency-blocked; preflight complete |
| SPEC-008 | dependency-blocked; preflight complete |
| SPEC-009 | dependency-blocked; preflight complete |
| SPEC-010 | dependency-blocked; preflight complete |
| SPEC-011 | dependency-blocked; preflight complete |
| SPEC-012 | dependency-blocked; preflight complete |
| SPEC-013 | dependency-blocked; preflight complete |
| SPEC-014 | dependency-blocked; preflight complete |
| SPEC-015 | dependency-blocked; release decision matrix preflight complete |
| SPEC-016 | `BLOCKED_BY_SPEC_001_THROUGH_SPEC_015`; release-cut checklist staged |
| SPEC-017 | blocked by released implementation contract + implementation attempt |
| SPEC-018 | blocked by release/conformance + operational evidence |
| SPEC-019 | future post-launch revision |

Worksheets: [SPEC-001.md](SPEC-001.md) through [SPEC-016.md](SPEC-016.md) where present.

**Preflight is not acceptance.** No owner checkbox has been completed by an agent and no stage lifecycle has advanced.

---

## 2. Canonical current conclusions — still draft

The preflight stack now specifies:

- referenced MVP visual/interaction fidelity with truthful production divergences;
- provider-neutral transportation/shelter/food/peer-support ports and Manual Adapter paths;
- stateless horizontal application semantics;
- durable async work and explicit backpressure;
- persistent command idempotency distinct from event identity;
- replay-safe Domain Event publication/outbox-equivalent semantics;
- single-use/shared auth challenge/session revocation semantics;
- atomic one-winner Case/assignment/Settlement operations;
- deterministic Support Signal/current Settlement/current assignment projections;
- first-class multi-cycle Settlement history;
- Follow-Up schedule identity and stale-job suppression;
- Notification logical-send dedupe distinct from transport/business retries;
- provider webhook/idempotency/reconciliation/privacy boundaries;
- bounded/paginated production API/query rules;
- scale/load targets owned by release-specific decisions rather than invented forecasts;
- resilience covering internal replay/staleness as well as external dependency failure;
- controlled pilot scope separated from traction/waitlist and technical capacity;
- operational analytics separated from clinical/causal claims;
- aggregate reporting privacy policy explicitly open as D-025.

These remain draft until sequential owner acceptance and SPEC-016 release.

---

## 3. Readiness gates

All remain `NOT_READY`:

`AUTH`, `CONSENT`, `CHECK-IN`, `COORDINATION`, `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SAFETY`, `PRIVACY`, `SCALE`, `RESILIENCE`, `OPERATIONS`, `REPORTING`.

A gate changes only with reproducible evidence under [TESTING.md](TESTING.md), not because preflight or implementation exists.

---

## 4. Open decisions

D-001 through D-025 remain governed by [DECISIONS.md](DECISIONS.md).

Key production decision groups:

- **Platform:** D-001–D-005;
- **Legal/privacy/operations:** D-006–D-009, D-013, D-025;
- **Product/safety:** D-010–D-016;
- **External fulfillment:** D-017–D-020;
- **Production workload/durability/SLO/recovery:** D-021–D-024.

No value/vendor/threshold is guessed.

SPEC-015 defines whether each release-relevant decision must close or may be safely deferred with the affected feature unavailable/manual-only/defaulted according to an accepted contract.

---

## 5. SPEC-006 reconciliation status

The earlier SPEC-006 handoff queue is now represented in the draft logical model:

- Support Signal computation identity/effective projection;
- event identity/idempotency/correlation/causation + replay-safe publication;
- one-active Case/assignment concurrency;
- provider/FulfillmentAttempt history;
- first-class Settlement cycles/current projection;
- Follow-Up blocking/carry-forward + schedule identity;
- persistent command idempotency;
- auth challenge/session state;
- Notification logical-send dedupe.

This representation is still unaccepted until SPEC-006 owner review.

---

## 6. Release boundary

SPEC-016 is the first possible implementation-authoritative cut.

It requires:
- owner-accepted prerequisite stages;
- D-001–D-025 release decision ledger from SPEC-015;
- pinned release/runtime artifacts;
- explicit enabled/manual-only/information-only/unavailable/future feature manifest;
- cross-artifact consistency review;
- owner lifecycle updates in STATUS/VERSIONING/CHANGELOG/release manifest.

Until that happens:

`IMPLEMENTATION_AUTHORITY = NOT_YET_RELEASED`

Release itself does **not** make the product production-ready; SPEC-017 implementation conformance and SPEC-018 launch readiness still follow.

---

## 7. Artifact status

All specification artifacts in this branch remain `draft`. Questionnaire content, production signal rules, consent templates, approved safety copy, provider choices, release workload targets, SLOs, recovery targets, and reporting privacy threshold remain unfinalized until their owning decisions/artifacts close.

---

## 8. Non-goals of current state

- no implementation authorization;
- no readiness claim;
- no owner acceptance performed by agents;
- no provider/cloud/queue/cache selection;
- no compliance/clinical claim;
- no capacity/recovery/reporting-threshold invention.
