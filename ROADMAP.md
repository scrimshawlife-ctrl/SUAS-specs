# ROADMAP.md — Specification-driven path to production

**Related:** [STATUS.md](STATUS.md), [VERSIONING.md](VERSIONING.md), [TESTING.md](TESTING.md), [DECISIONS.md](DECISIONS.md)

This is an ordered specification-and-handoff roadmap. It follows the repository authority rule: define governing product constraints first, specify behavior/contracts, then architecture/technical plan, then verification/handoff. Implementation authority remains `NOT_YET_RELEASED` until the relevant specs are `released`.

Current phase: `SPECIFICATION_BOOTSTRAP`. SPEC-001 remains `READY_FOR_REVIEW`; it is not `accepted`.

The roadmap was expanded before first release to account for observed product traction and the need to preserve the referenced MVP while avoiding provider/vendor and pilot-scale lock-in.

---

## SPEC-001 — Accept product and authority set

- **Status:** `READY_FOR_REVIEW`.
- **Objective:** Accept [README.md](README.md), [PRODUCT.md](PRODUCT.md), [GLOSSARY.md](GLOSSARY.md), [AGENTS.md](AGENTS.md), [VERSIONING.md](VERSIONING.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md).
- **Exit:** terminology/authority frozen for v0.1; owner checklist complete.
- **Handoff:** none.
- **Acceptance:** only `@scrimshawlife-ctrl`; agents do not self-accept.

## SPEC-002 — Accept consent, privacy, safety, security boundaries

- **Dependencies:** SPEC-001.
- **Required:** [CONSENT.md](CONSENT.md), [PRIVACY.md](PRIVACY.md), [SAFETY.md](SAFETY.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [SECURITY.md](SECURITY.md).
- **Exit:** consent/use-time rules and safety non-goals accepted; HIPAA remains D-006 unless decided; safety copy may remain D-012 but ship constraint explicit.
- **Handoff:** auth/consent/safety implementation only after release.

## SPEC-003 — Accept check-in and Support Signal contracts

- **Dependencies:** SPEC-001, SPEC-002.
- **Required:** [CHECKINS.md](CHECKINS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [EVENT_MODEL.md](EVENT_MODEL.md).
- **Exit:** versioning/determinism contract accepted; D-011 either decided later or explicit non-ship fixture boundary.

## SPEC-004 — Accept Case, Service Request, responder workflow contracts

- **Dependencies:** SPEC-001, SPEC-002.
- **Required:** [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md).
- **Exit:** states/transitions frozen; contested claims/assignments have an atomicity requirement; assignment ≠ fulfillment.

## SPEC-005 — Accept resources, referrals, fulfillment, follow-up, settlement

- **Dependencies:** SPEC-004.
- **Required:** [RESOURCES.md](RESOURCES.md), [REFERRALS.md](REFERRALS.md), [FULFILLMENT.md](FULFILLMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [SETTLEMENT.md](SETTLEMENT.md).
- **Exit:** Referral/Assignment/Fulfillment/Settlement distinctions accepted; Fulfillment Attempt concept reconciled; funding remains future.

## SPEC-006 — Accept domain, data, event models

- **Dependencies:** SPEC-001 through SPEC-005.
- **Required:** [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [EVENT_MODEL.md](EVENT_MODEL.md), [ARCHITECTURE.md](ARCHITECTURE.md).
- **Exit:** entities/ownership/tenancy/immutability align with all accepted domain specs, including Fulfillment Attempts/provider integration metadata.

## SPEC-007 — Accept core architecture, API, auth, notifications, admin

- **Dependencies:** SPEC-006.
- **Required:** [ARCHITECTURE.md](ARCHITECTURE.md), [API.md](API.md), [APIS.md](APIS.md), [AUTH.md](AUTH.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [ADMIN.md](ADMIN.md).
- **Exit:** scalable modular monolith confirmed; no vendor locked; API paths/idempotency/pagination rules accepted; org-admin ≠ SUAS-admin.

## SPEC-008 — Accept MVP visual and interaction conformance

- **Dependencies:** SPEC-001, SPEC-002, SPEC-007.
- **Required:** [MVP_REFERENCE.md](MVP_REFERENCE.md), [PRODUCT.md](PRODUCT.md), [ONBOARDING.md](ONBOARDING.md).
- **Objective:** make the referenced MVP the explicit production visual/interaction source of truth while defining safe production divergences.
- **Exit:** required surfaces and conformance classes accepted; category/display mapping accepted; accessibility target accepted; visual-regression fixture contract accepted.
- **Handoff:** frontend may implement against MVP reference without silently redesigning the product.

## SPEC-009 — Accept provider-neutral service fulfillment

- **Dependencies:** SPEC-004 through SPEC-007.
- **Required:** [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [APIS.md](APIS.md), [FULFILLMENT.md](FULFILLMENT.md), [CONSENT.md](CONSENT.md), [SECURITY.md](SECURITY.md).
- **Objective:** ensure rides, temporary rooms/shelter, food, and peer support remain provider-agnostic and manual-provider capable.
- **Exit:** four MVP capability ports accepted; Manual Adapter required; provider status normalization/idempotency/webhook/consent rules accepted; D-017–D-020 may remain open.
- **Handoff:** adapters may be implemented independently of domain state machines.

## SPEC-010 — Accept scaling contract

- **Dependencies:** SPEC-006, SPEC-007, SPEC-009.
- **Required:** [SCALING.md](SCALING.md), [ARCHITECTURE.md](ARCHITECTURE.md), [DATA_MODEL.md](DATA_MODEL.md), [APIS.md](APIS.md).
- **Objective:** preserve simple architecture while preventing avoidable pilot-scale ceilings.
- **Exit:** stateless application invariant, durable async requirement, atomic contested commands, bounded APIs, DB scaling doctrine, tenant fairness, observability, load profiles accepted.
- **Handoff:** performance/load implementation and instrumentation; D-021/D-023 may remain open until release plan.

## SPEC-011 — Accept resilience/degradation contract

- **Dependencies:** SPEC-007, SPEC-009, SPEC-010.
- **Required:** [RESILIENCE.md](RESILIENCE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md).
- **Exit:** timeout/retry/dead-letter/circuit/unknown-outcome/backpressure/restore/failure-drill rules accepted; D-024 may remain release-specific.

## SPEC-012 — Accept testing and readiness gates

- **Dependencies:** SPEC-002 through SPEC-011.
- **Required:** [TESTING.md](TESTING.md), [STATUS.md](STATUS.md).
- **Exit:** gate names frozen for release: AUTH, CONSENT, CHECK-IN, COORDINATION, EXTERNAL_FULFILLMENT, UI_CONFORMANCE, SAFETY, PRIVACY, SCALE, RESILIENCE, OPERATIONS, REPORTING.
- **Handoff:** implementation test plan must map evidence to every applicable gate.

## SPEC-013 — Accept deployment, operations, incident/recovery

- **Dependencies:** SPEC-007, SPEC-010, SPEC-011, SPEC-012.
- **Required:** [DEPLOYMENT.md](DEPLOYMENT.md), [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [SECURITY.md](SECURITY.md).
- **Exit:** environments defined; production-critical jobs are durable; provider/queue/DB observability and recovery ownership specified; exact vendors remain decisions.

## SPEC-014 — Accept pilot and analytics contracts

- **Dependencies:** SPEC-001, SPEC-012, SPEC-013.
- **Required:** [PILOT.md](PILOT.md), [ANALYTICS.md](ANALYTICS.md).
- **Exit:** pilot remains ~25–50 veterans in Santa Clara County unless changed; scale capacity envelopes are not adoption forecasts; operational metrics remain non-clinical.

## SPEC-015 — Close or explicitly defer release-blocking decisions

- **Dependencies:** relevant preceding specs.
- **Required:** [DECISIONS.md](DECISIONS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [SAFETY.md](SAFETY.md), deployment/provider/scale/resilience specs.
- **Objective:** distinguish decisions that must close for the first production release from decisions that may safely remain open.
- **At minimum evaluate:** D-001–D-005, D-011–D-014, D-017–D-024.
- **Exit:** each is either decided or explicitly deferred with a non-ship/feature-unavailable boundary. No invented providers/SLOs/RTO/RPO.

## SPEC-016 — First released specification cut

- **Dependencies:** SPEC-001 through SPEC-015 as applicable; unresolved decisions must have explicit safe deferral.
- **Required:** entire stack, [CHANGELOG.md](CHANGELOG.md), [STATUS.md](STATUS.md).
- **Exit:** lifecycle `released`; implementation authority becomes `RELEASED_FOR_IMPLEMENTATION` for named artifacts/version.
- **Handoff:** `SUAS` may implement only against that released cut.

## SPEC-017 — Implementation conformance review

- **Dependencies:** SPEC-016 + implementation attempt.
- **Objective:** compare implementation with released specs, including MVP visuals/provider ports/scale/resilience.
- **Exit:** gap list reconciled back into specs; no silent spec-from-code.

## SPEC-018 — Pilot / production readiness review

- **Dependencies:** SPEC-016, SPEC-017, implementation and operating plan.
- **Required:** [TESTING.md](TESTING.md), [STATUS.md](STATUS.md), [PILOT.md](PILOT.md), [OPERATIONS.md](OPERATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md).
- **Exit:** every gate required by the target launch is `READY` or has an explicit owner-approved spec waiver; provider/environment decisions for enabled features are closed; load/failure/restore evidence attached.
- **Handoff:** go/no-go.

## SPEC-019 — Post-pilot / post-launch revision

- **Dependencies:** completed or exited pilot/launch cycle.
- **Objective:** incorporate observed feedback/scale data without silent clinical, billing, or vendor-domain expansion.
- **Exit:** next revision released; measured architecture changes only.

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

No implementation milestone may use the existence of an MVP or provider prototype to skip the corresponding accepted/released spec contract.
