# ROADMAP.md — SPEC-001 through SPEC-015

**Related:** [STATUS.md](STATUS.md), [VERSIONING.md](VERSIONING.md), [TESTING.md](TESTING.md), [DECISIONS.md](DECISIONS.md)

This is an ordered specification-and-handoff roadmap, not a wishlist. Each item has an objective, dependencies, required specs, exit criteria, and an implementation handoff. Implementation authority remains `NOT_YET_RELEASED` until the relevant specs are `released`.

Current phase: `SPECIFICATION_BOOTSTRAP`. SPEC-001 is `READY_FOR_REVIEW` ([SPEC-001.md](SPEC-001.md)); it is not `accepted`. Owner review is the next step, **not** implementation. See [SPEC_AUDIT.md](SPEC_AUDIT.md).

---

## SPEC-001 — Accept the v0.1 product and authority set

- **Status:** `READY_FOR_REVIEW` (not `accepted`; not `released`). Worksheet: [SPEC-001.md](SPEC-001.md).
- **Objective:** Review and accept [README.md](README.md), [PRODUCT.md](PRODUCT.md), [GLOSSARY.md](GLOSSARY.md), [AGENTS.md](AGENTS.md), [VERSIONING.md](VERSIONING.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md).
- **Dependencies:** This bootstrap stack exists (`OBSERVED`).
- **Required specs:** the files listed above.
- **Exit criteria:** Those files marked `accepted` for stack `0.1.0`; terminology frozen; no remaining aliasing of non-interchangeable concepts.
- **Implementation handoff:** None. Implementation must not start on product identity alone.
- **Acceptance:** only `@scrimshawlife-ctrl` may set lifecycle to `accepted` after the [SPEC-001.md](SPEC-001.md) checklist is complete. Agents must not self-accept.

## SPEC-002 — Accept consent, privacy, and safety boundaries

- **Objective:** Accept [CONSENT.md](CONSENT.md), [PRIVACY.md](PRIVACY.md), [SAFETY.md](SAFETY.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [SECURITY.md](SECURITY.md).
- **Dependencies:** SPEC-001.
- **Required specs:** files listed above, plus [GLOSSARY.md](GLOSSARY.md).
- **Exit criteria:** Consent grant shapes accepted; red-state non-goals accepted; `HIPAA_APPLICABILITY` remains `DECISION_PENDING` unless D-006 is closed; approved safety copy still `DECISION_PENDING` (D-012) but the *behavior* (surface approved resources, human review, consent, audit) is accepted.
- **Implementation handoff:** After `released`, AUTH/CONSENT/SAFETY work in `SUAS` may begin against those contracts only.

## SPEC-003 — Accept check-in and support-signal contracts

- **Objective:** Accept [CHECKINS.md](CHECKINS.md) and [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md) as contracts, including versioning and determinism requirements.
- **Dependencies:** SPEC-001, SPEC-002.
- **Required specs:** [CHECKINS.md](CHECKINS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [EVENT_MODEL.md](EVENT_MODEL.md).
- **Exit criteria:** Questionnaire versioning rules accepted. Signal computation contract accepted. Exact scoring rules may remain D-011 `DECISION_PENDING`; if so, implementation may build the engine and fixtures but must not ship thresholds as if decided.
- **Implementation handoff:** Signal engine and questionnaire modules in `SUAS`, citing artifact versions.

## SPEC-004 — Accept case, dispatch, and responder workflow contracts

- **Objective:** Accept [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md).
- **Dependencies:** SPEC-001, SPEC-002.
- **Required specs:** those three plus [DOMAIN_MODEL.md](DOMAIN_MODEL.md).
- **Exit criteria:** State names and transitions frozen. Hidden transitions forbidden. Assignment ≠ fulfillment restated and accepted.
- **Implementation handoff:** Case and request state machines in `SUAS`.

## SPEC-005 — Accept resources, referrals, fulfillment, follow-up, settlement

- **Objective:** Accept [RESOURCES.md](RESOURCES.md), [REFERRALS.md](REFERRALS.md), [FULFILLMENT.md](FULFILLMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [SETTLEMENT.md](SETTLEMENT.md).
- **Dependencies:** SPEC-004.
- **Required specs:** files listed above.
- **Exit criteria:** Referral ≠ Service Request; fulfillment confirmation rules; follow-up first-class; settlement not clinical; funding path `FUTURE`.
- **Implementation handoff:** Resource catalog, referral, fulfillment, follow-up, settlement modules.

## SPEC-006 — Accept domain, data, and event models

- **Objective:** Accept [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [EVENT_MODEL.md](EVENT_MODEL.md).
- **Dependencies:** SPEC-001 through SPEC-005 (content must match).
- **Required specs:** those three plus [ARCHITECTURE.md](ARCHITECTURE.md).
- **Exit criteria:** Entity list complete vs domain specs; mutable vs immutable distinguished; event envelope frozen at `schema_version` 0.1.0 when released.
- **Implementation handoff:** Schema migrations and event emission in `SUAS`.

## SPEC-007 — Accept architecture, API, auth, notifications, admin

- **Objective:** Accept [ARCHITECTURE.md](ARCHITECTURE.md), [API.md](API.md), [AUTH.md](AUTH.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [ADMIN.md](ADMIN.md).
- **Dependencies:** SPEC-006.
- **Required specs:** files listed above.
- **Exit criteria:** Modular monolith confirmed; no cloud vendor locked; API paths and error/idempotency rules accepted; notification channels MVP = EMAIL, SMS, IN_APP; org-admin ≠ SUAS-admin.
- **Implementation handoff:** Application module layout, API surface, auth, notifications, admin.

## SPEC-008 — Accept testing and MVP acceptance gate

- **Objective:** Accept [TESTING.md](TESTING.md) and the gate table in [STATUS.md](STATUS.md).
- **Dependencies:** SPEC-002 through SPEC-007.
- **Required specs:** [TESTING.md](TESTING.md), [STATUS.md](STATUS.md).
- **Exit criteria:** Critical suites listed and mapped to specs. Gate names frozen: AUTH, CONSENT, CHECK-IN, COORDINATION, SAFETY, PRIVACY, OPERATIONS, REPORTING.
- **Implementation handoff:** Test plan in `SUAS` citing this file.

## SPEC-009 — Accept deployment, operations, incident response

- **Objective:** Accept [DEPLOYMENT.md](DEPLOYMENT.md), [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md).
- **Dependencies:** SPEC-007, SPEC-008.
- **Required specs:** those three plus [SECURITY.md](SECURITY.md).
- **Exit criteria:** Environments LOCAL/TEST/STAGING/PRODUCTION defined; cloud still `DECISION_PENDING` unless D-001/D-005 closed; technical vs veteran-support ops separated; incident path does not invent legal deadlines.
- **Implementation handoff:** Environment topology (without locking a vendor) and runbooks.

## SPEC-010 — Accept pilot and analytics contracts

- **Objective:** Accept [PILOT.md](PILOT.md) and [ANALYTICS.md](ANALYTICS.md).
- **Dependencies:** SPEC-001, SPEC-008, SPEC-009.
- **Required specs:** those two.
- **Exit criteria:** 25–50 veterans, Santa Clara County; partners remain placeholders unless D-008 closed; metrics list excludes clinical-efficacy and suicides-prevented.
- **Implementation handoff:** Pilot configuration and operational metric emission.

## SPEC-011 — Close or explicitly defer D-011 and D-012

- **Objective:** Either specify exact Support Signal scoring rules and approved safety copy, or formally defer them with test-fixture placeholders.
- **Dependencies:** SPEC-003, SPEC-002.
- **Required specs:** [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [SAFETY.md](SAFETY.md), [DECISIONS.md](DECISIONS.md).
- **Exit criteria:** D-011 and D-012 either decided (with versioned fixtures) or deferred with an explicit non-ship constraint.
- **Implementation handoff:** Signal fixtures and safety-copy templates, only if decided.

## SPEC-012 — First `released` specification cut

- **Objective:** Promote the accepted 0.1.x stack (or a 0.2.0 if breaking edits occurred) to `released`.
- **Dependencies:** SPEC-001 through SPEC-010; SPEC-011 either closed or deferred.
- **Required specs:** entire stack; [CHANGELOG.md](CHANGELOG.md); [STATUS.md](STATUS.md).
- **Exit criteria:** Lifecycle = `released`. Implementation authority changes from `NOT_YET_RELEASED` to `RELEASED_FOR_IMPLEMENTATION` (name recorded in STATUS at that time).
- **Implementation handoff:** `SUAS` may implement against the released cut. PRs must cite.

## SPEC-013 — Implementation conformance review (specs side)

- **Objective:** Review `SUAS` implementation citations and return gaps to specs.
- **Dependencies:** SPEC-012 and an implementation attempt.
- **Required specs:** whichever files the gaps touch.
- **Exit criteria:** Gap list filed; no silent spec-from-code.
- **Implementation handoff:** Spec patches, then implementation fixes.

## SPEC-014 — Pilot readiness review

- **Objective:** Evaluate AUTH through REPORTING gates against a built system and a staffed pilot plan.
- **Dependencies:** SPEC-012, SPEC-013, closed or deferred D-008 and D-009.
- **Required specs:** [PILOT.md](PILOT.md), [TESTING.md](TESTING.md), [STATUS.md](STATUS.md), [OPERATIONS.md](OPERATIONS.md).
- **Exit criteria:** All eight MVP gates `READY` or an explicit waiver recorded as a spec change. Pilot readiness may then move off `NOT_READY`.
- **Implementation handoff:** Go/no-go for the Santa Clara County pilot.

## SPEC-015 — Post-pilot specification revision

- **Objective:** Incorporate pilot feedback without silently expanding scope (no new clinical claims, no billing assertion, no automated dispatch).
- **Dependencies:** A completed or exited pilot per [PILOT.md](PILOT.md).
- **Required specs:** those changed by feedback; [ANALYTICS.md](ANALYTICS.md); [CHANGELOG.md](CHANGELOG.md).
- **Exit criteria:** Revision released; `FUTURE` items (BENEFITS, HOUSING, HEALTHCARE_NAVIGATION, COMMUNITY, billing adapter) remain future unless explicitly promoted.
- **Implementation handoff:** Next released contract for `SUAS`.

---

## Ordering constraints

```
SPEC-001
  ├── SPEC-002 ── SPEC-003 ── SPEC-011
  └── SPEC-004 ── SPEC-005
         └── SPEC-006 ── SPEC-007 ── SPEC-008 ── SPEC-009 ── SPEC-010
                                └── SPEC-012 ── SPEC-013 ── SPEC-014 ── SPEC-015
```

SPEC-011 may complete in parallel with SPEC-004–010 but must be closed or deferred before SPEC-012.
