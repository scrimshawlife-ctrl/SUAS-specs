# ROADMAP.md — Specification-driven path to production

**Related:** [STATUS.md](STATUS.md), [VERSIONING.md](VERSIONING.md), [TESTING.md](TESTING.md), [DECISIONS.md](DECISIONS.md)

Governing constraints come first, then behavior/contracts, logical/technical plan, verification, release, implementation conformance, and readiness. Implementation authority remains `NOT_YET_RELEASED` until SPEC-016 is actually released by the owner.

Preflight may prepare blocked worksheets and repair draft contradictions. It never satisfies dependencies or changes lifecycle.

---

## SPEC-001 — Product and authority set
- **Status:** `READY_FOR_REVIEW`
- **Worksheet:** [SPEC-001.md](SPEC-001.md)
- **Required:** README, PRODUCT, GLOSSARY, AGENTS, VERSIONING, STATUS, DECISIONS
- **Exit:** owner accepts terminology/authority/governing principles.

## SPEC-002 — Consent, privacy, safety, security
- **Status:** `BLOCKED_BY_SPEC_001`; preflight complete
- **Worksheet:** [SPEC-002.md](SPEC-002.md)
- **Required:** CONSENT, PRIVACY, SAFETY, TRUSTED_CIRCLE, SECURITY
- **Exit:** use-time consent/provider disclosure/safety/security boundaries accepted.

## SPEC-003 — Check-In, Support Signal, events
- **Status:** `BLOCKED_BY_SPEC_001_AND_SPEC_002`; preflight complete
- **Worksheet:** [SPEC-003.md](SPEC-003.md)
- **Exit:** questionnaire/versioning, deterministic replay-safe signal settlement, event identity/publication semantics accepted; D-011 remains explicit.

## SPEC-004 — Case, Service Request, responder workflow
- **Status:** `BLOCKED_BY_SPEC_001_AND_SPEC_002`; preflight complete
- **Worksheet:** [SPEC-004.md](SPEC-004.md)
- **Exit:** canonical state machines, one-winner concurrency, stale-state/idempotency rules accepted.

## SPEC-005 — Resources, Referral, Fulfillment, Follow-Up, Settlement
- **Status:** `BLOCKED_BY_SPEC_004`; preflight complete
- **Worksheet:** [SPEC-005.md](SPEC-005.md)
- **Exit:** resource/live availability distinction, replay-safe Referral/Fulfillment, Follow-Up durable schedule semantics, multi-cycle Settlement history accepted.

## SPEC-006 — Domain/data/event/architecture reconciliation
- **Status:** `BLOCKED_BY_SPEC_001_THROUGH_SPEC_005`; preflight complete
- **Worksheet:** [SPEC-006.md](SPEC-006.md)
- **Exit:** entity ownership, tenancy, deterministic current projections, persistent idempotency, Settlement cycles, Follow-Up stale-job identity, provider-attempt separation accepted as one logical model.

## SPEC-007 — Core architecture, API, auth, notifications, admin
- **Status:** `BLOCKED_BY_SPEC_006`; preflight complete
- **Worksheet:** [SPEC-007.md](SPEC-007.md)
- **Exit:** `/api/v0` contract, persistent command idempotency, horizontal auth/session semantics, durable logical notifications, provider-neutral admin configuration accepted.

## SPEC-008 — MVP visual/interaction conformance
- **Status:** `BLOCKED_BY_SPEC_001_SPEC_002_SPEC_007`; preflight complete
- **Worksheet:** [SPEC-008.md](SPEC-008.md)
- **Exit:** observed MVP hierarchy/QRF/resources/responder/admin surfaces, truthful production divergences, accessibility, visual-regression contract accepted.

## SPEC-009 — Provider-neutral service fulfillment
- **Status:** `BLOCKED_BY_SPEC_004_THROUGH_SPEC_007`; preflight complete
- **Worksheet:** [SPEC-009.md](SPEC-009.md)
- **Exit:** four MVP capability ports, Manual Adapter, provider normalization/idempotency/webhook/privacy/replacement semantics accepted.

## SPEC-010 — Scaling
- **Status:** `BLOCKED_BY_SPEC_006_SPEC_007_SPEC_009`; preflight complete
- **Worksheet:** [SPEC-010.md](SPEC-010.md)
- **Exit:** stateless app, durable work, atomic commands, bounded APIs, PostgreSQL doctrine, tenant fairness, load evidence accepted. D-021/D-023 own numeric release targets.

## SPEC-011 — Resilience/degradation
- **Status:** `BLOCKED_BY_SPEC_007_SPEC_009_SPEC_010`; preflight complete
- **Worksheet:** [SPEC-011.md](SPEC-011.md)
- **Exit:** crash/replay/stale-work/session/event/provider/restore failure semantics accepted; D-024 owns RTO/RPO.

## SPEC-012 — Testing/readiness gates
- **Status:** `BLOCKED_BY_SPEC_002_THROUGH_SPEC_011`; preflight complete
- **Worksheet:** [SPEC-012.md](SPEC-012.md)
- **Exit:** reproducible evidence contract accepted for all 12 gates.

## SPEC-013 — Deployment, operations, incident/recovery
- **Status:** `BLOCKED_BY_SPEC_007_SPEC_010_SPEC_011_SPEC_012`; preflight complete
- **Worksheet:** [SPEC-013.md](SPEC-013.md)
- **Exit:** environments, provider/queue/DB operations, incident evidence, recovery ownership accepted without vendor/legal-timeline invention.

## SPEC-014 — Controlled pilot and analytics
- **Status:** `BLOCKED_BY_SPEC_001_SPEC_012_SPEC_013`; preflight complete
- **Worksheet:** [SPEC-014.md](SPEC-014.md)
- **Exit:** controlled cohort vs traction boundary, provider/operations readiness, exact operational metric definitions, forbidden clinical claims, D-025 reporting privacy boundary accepted.

## SPEC-015 — Release-blocking decisions / safe deferrals
- **Status:** `BLOCKED_BY_RELEVANT_SPEC_001_THROUGH_SPEC_014`; preflight complete
- **Worksheet:** [SPEC-015.md](SPEC-015.md)
- **Decision scope:** D-001 through D-025
- **Exit:** every release-relevant decision is explicitly `DECIDED` or safely `DEFERRED_FOR_RELEASE` with a feature-unavailable/manual/default boundary permitted by accepted specs.

## SPEC-016 — First released specification cut
- **Status:** `BLOCKED_BY_SPEC_001_THROUGH_SPEC_015`
- **Dependencies:** accepted prerequisite stages + SPEC-015 decision ledger
- **Required:** named release artifact set, CHANGELOG, STATUS, VERSIONING, release manifest/checklist
- **Exit:** owner changes named artifacts/version to `released`; implementation authority becomes `RELEASED_FOR_IMPLEMENTATION` only for that released cut.

## SPEC-017 — Implementation conformance review
- **Status:** blocked by SPEC-016 + implementation attempt
- **Exit:** implementation compared against released contracts; gaps return to specs.

## SPEC-018 — Pilot / production readiness
- **Status:** blocked by SPEC-016/SPEC-017 + operating evidence
- **Exit:** launch-required gates READY or explicit owner-approved spec waivers; enabled decisions closed; load/failure/restore/ops evidence attached.

## SPEC-019 — Post-pilot / post-launch revision
- **Status:** future
- **Exit:** measured feedback/scale findings become a new released revision through the same governance model.

---

## Ordering constraint

```text
SPEC-001
  |-- SPEC-002 -- SPEC-003
  |-- SPEC-004 -- SPEC-005
                 |
                 SPEC-006 -- SPEC-007
                                |-- SPEC-008
                                |-- SPEC-009 -- SPEC-010 -- SPEC-011
                                                  |          |
                                                  +-- SPEC-012
                                                         |
                                                     SPEC-013
                                                         |
                                                     SPEC-014
                                                         |
                                                     SPEC-015
                                                         |
                                                     SPEC-016 RELEASE
                                                         |
                                                     SPEC-017
                                                         |
                                                     SPEC-018
                                                         |
                                                     SPEC-019
```

No MVP/prototype/provider response/queue message/browser state/physical DB shortcut or implementation default may bypass an accepted/released contract.
