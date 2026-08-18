# ROADMAP.md — Specification-driven path to production

**Related:** [STATUS.md](STATUS.md), [VERSIONING.md](VERSIONING.md), [TESTING.md](TESTING.md), [DECISIONS.md](DECISIONS.md)

This is an ordered specification-and-handoff roadmap. Governing constraints come first, then behavior/contracts, technical plan, verification, release, conformance, and readiness. Implementation authority remains `NOT_YET_RELEASED` until a released specification cut exists.

Current phase: `SPECIFICATION_BOOTSTRAP`.

Preflight work may prepare later worksheets and repair draft cross-artifact contradictions, but **must not bypass dependencies or change lifecycle**.

---

## SPEC-001 — Accept product and authority set

- **Status:** `READY_FOR_REVIEW`.
- **Worksheet:** [SPEC-001.md](SPEC-001.md).
- **Objective:** Accept [README.md](README.md), [PRODUCT.md](PRODUCT.md), [GLOSSARY.md](GLOSSARY.md), [AGENTS.md](AGENTS.md), [VERSIONING.md](VERSIONING.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md).
- **Exit:** terminology/authority frozen for v0.1; owner checklist complete.
- **Handoff:** none.
- **Acceptance:** owner-only.

## SPEC-002 — Accept consent, privacy, safety, security boundaries

- **Status:** `BLOCKED_BY_SPEC_001`; preflight complete.
- **Worksheet:** [SPEC-002.md](SPEC-002.md).
- **Dependencies:** SPEC-001.
- **Required:** [CONSENT.md](CONSENT.md), [PRIVACY.md](PRIVACY.md), [SAFETY.md](SAFETY.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [SECURITY.md](SECURITY.md).
- **Exit:** use-time consent, provider disclosure/minimization, safety non-goals, security boundaries accepted; D-006/D-012 handled per release rules.
- **Handoff:** none until release.

## SPEC-003 — Accept Check-In / Support Signal / event contracts

- **Status:** `BLOCKED_BY_SPEC_001_AND_SPEC_002`; preflight complete.
- **Worksheet:** [SPEC-003.md](SPEC-003.md).
- **Dependencies:** SPEC-001, SPEC-002.
- **Required:** [CHECKINS.md](CHECKINS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [EVENT_MODEL.md](EVENT_MODEL.md).
- **Exit:** questionnaire/versioning rules; deterministic/idempotently settled Support Signal engine; replay-safe event semantics accepted; D-011 remains open or is separately decided/deferred; effective-signal data projection handed to SPEC-006.

## SPEC-004 — Accept Case / Service Request / responder workflow contracts

- **Status:** `BLOCKED_BY_SPEC_001_AND_SPEC_002`; preflight complete.
- **Worksheet:** [SPEC-004.md](SPEC-004.md).
- **Dependencies:** SPEC-001, SPEC-002.
- **Required:** [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md).
- **Exit:** state names/transitions frozen; atomic one-winner claim/assignment and idempotency semantics accepted; provider evidence cannot bypass canonical Request/Fulfillment transitions; schema representation questions handed to SPEC-006.

## SPEC-005 — Accept resources, referrals, fulfillment, follow-up, settlement

- **Status:** `BLOCKED_BY_SPEC_004`; preflight complete.
- **Worksheet:** [SPEC-005.md](SPEC-005.md).
- **Dependencies:** SPEC-004.
- **Required:** [RESOURCES.md](RESOURCES.md), [REFERRALS.md](REFERRALS.md), [FULFILLMENT.md](FULFILLMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [SETTLEMENT.md](SETTLEMENT.md).
- **Exit:** Resource/live-availability distinction; Referral send idempotency; Fulfillment Attempt/provider/manual semantics; Follow-Up retry/durable due semantics; multi-cycle Settlement history accepted; remaining schema representation handed to SPEC-006; funding remains future.

## SPEC-006 — Accept domain, data, event models

- **Dependencies:** SPEC-001 through SPEC-005.
- **Required:** [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [EVENT_MODEL.md](EVENT_MODEL.md), [ARCHITECTURE.md](ARCHITECTURE.md).
- **Exit:** entities/ownership/tenancy/immutability align with accepted domain specs, including signal computation identity, Fulfillment Attempts, provider metadata, command/event idempotency, first-class Settlement resolution history, Follow-Up blocking/carry-forward rules, and deterministic current projections.

## SPEC-007 — Accept core architecture, API, auth, notifications, admin

- **Dependencies:** SPEC-006.
- **Required:** [ARCHITECTURE.md](ARCHITECTURE.md), [API.md](API.md), [APIS.md](APIS.md), [AUTH.md](AUTH.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [ADMIN.md](ADMIN.md).
- **Exit:** scalable modular monolith; vendor neutrality; API command/idempotency/pagination rules; role boundaries accepted.

## SPEC-008 — Accept MVP visual and interaction conformance

- **Dependencies:** SPEC-001, SPEC-002, SPEC-007.
- **Required:** [MVP_REFERENCE.md](MVP_REFERENCE.md), [PRODUCT.md](PRODUCT.md), [ONBOARDING.md](ONBOARDING.md).
- **Objective:** referenced MVP is explicit production visual/interaction source of truth with safe production divergences.
- **Exit:** surfaces/conformance classes/category mapping/accessibility/visual-regression contract accepted.

## SPEC-009 — Accept provider-neutral service fulfillment

- **Dependencies:** SPEC-004 through SPEC-007.
- **Required:** [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [APIS.md](APIS.md), [FULFILLMENT.md](FULFILLMENT.md), [CONSENT.md](CONSENT.md), [SECURITY.md](SECURITY.md).
- **Exit:** four MVP capability ports; Manual Adapter; normalization/idempotency/webhook/consent rules accepted; D-017–D-020 may remain open.

## SPEC-010 — Accept scaling contract

- **Dependencies:** SPEC-006, SPEC-007, SPEC-009.
- **Required:** [SCALING.md](SCALING.md), [ARCHITECTURE.md](ARCHITECTURE.md), [DATA_MODEL.md](DATA_MODEL.md), [APIS.md](APIS.md).
- **Exit:** stateless app invariant, durable async, atomic contested commands, bounded APIs, DB doctrine, tenant fairness, observability/load profiles accepted.

## SPEC-011 — Accept resilience/degradation contract

- **Dependencies:** SPEC-007, SPEC-009, SPEC-010.
- **Required:** [RESILIENCE.md](RESILIENCE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md).
- **Exit:** timeout/retry/dead-letter/circuit/unknown-outcome/backpressure/restore/failure-drill rules accepted.

## SPEC-012 — Accept testing and readiness gates

- **Dependencies:** SPEC-002 through SPEC-011.
- **Required:** [TESTING.md](TESTING.md), [STATUS.md](STATUS.md).
- **Exit:** gate names frozen: AUTH, CONSENT, CHECK-IN, COORDINATION, EXTERNAL_FULFILLMENT, UI_CONFORMANCE, SAFETY, PRIVACY, SCALE, RESILIENCE, OPERATIONS, REPORTING.

## SPEC-013 — Accept deployment, operations, incident/recovery

- **Dependencies:** SPEC-007, SPEC-010, SPEC-011, SPEC-012.
- **Required:** [DEPLOYMENT.md](DEPLOYMENT.md), [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [SECURITY.md](SECURITY.md).
- **Exit:** environments, durable production jobs, observability/recovery ownership accepted; exact vendors may remain release decisions.

## SPEC-014 — Accept pilot and analytics contracts

- **Dependencies:** SPEC-001, SPEC-012, SPEC-013.
- **Required:** [PILOT.md](PILOT.md), [ANALYTICS.md](ANALYTICS.md).
- **Exit:** controlled pilot scope remains explicit; scale envelopes are not forecasts; operational metrics remain non-clinical.

## SPEC-015 — Close or explicitly defer release-blocking decisions

- **Dependencies:** relevant preceding specs.
- **Required:** [DECISIONS.md](DECISIONS.md) plus affected specs.
- **At minimum evaluate:** D-001–D-005, D-011–D-014, D-017–D-024.
- **Exit:** each release-relevant decision is decided or explicitly deferred with non-ship/feature-unavailable boundary.

## SPEC-016 — First released specification cut

- **Dependencies:** SPEC-001 through SPEC-015 as applicable.
- **Required:** entire released set, [CHANGELOG.md](CHANGELOG.md), [STATUS.md](STATUS.md).
- **Exit:** lifecycle `released`; named artifacts/version become `RELEASED_FOR_IMPLEMENTATION`.

## SPEC-017 — Implementation conformance review

- **Dependencies:** SPEC-016 + implementation attempt.
- **Exit:** implementation compared with released specs including MVP visuals/provider ports/scale/resilience; gaps return to specs.

## SPEC-018 — Pilot / production readiness review

- **Dependencies:** SPEC-016, SPEC-017, implementation and operating plan.
- **Required:** testing/status/pilot/operations/scale/resilience evidence.
- **Exit:** required gates READY or explicit owner-approved spec waiver; enabled provider/environment decisions closed; load/failure/restore evidence attached.

## SPEC-019 — Post-pilot / post-launch revision

- **Dependencies:** completed/exited launch cycle.
- **Exit:** measured feedback/scale changes incorporated through a new released revision.

---

## Ordering constraints

```text
SPEC-001
  |-- SPEC-002 -- SPEC-003
  |-- SPEC-004 -- SPEC-005
                 |
                 SPEC-006 -- SPEC-007
                                |-- SPEC-008  MVP visual contract
                                |-- SPEC-009  provider ports
                                      |
                                      SPEC-010  scale
                                      |
                                      SPEC-011  resilience
                                           |
                                           SPEC-012 testing/gates
                                           |
                                           SPEC-013 deployment/ops
                                           |
                                           SPEC-014 pilot/analytics
                                           |
                                           SPEC-015 release decisions
                                           |
                                           SPEC-016 RELEASE
                                           |
                                           SPEC-017 conformance
                                           |
                                           SPEC-018 readiness
                                           |
                                           SPEC-019 revision
```

No implementation milestone may use an MVP, prototype, provider response, queue message, or browser state to skip the corresponding accepted/released specification contract.
