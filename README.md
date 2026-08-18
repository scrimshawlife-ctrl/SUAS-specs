# Shut Up and Serve (SUAS) — Specification v0.1

**Product:** Shut Up and Serve  
**System identifier:** SUAS  
**Specification version:** 0.1.0  
**Specification status:** `draft`  
**Phase:** `SPECIFICATION_BOOTSTRAP`  
**Implementation authority:** `NOT_YET_RELEASED`

This repository (`SUAS-specs`) is the canonical specification authority for Shut Up and Serve. The implementation repository (`scrimshawlife-ctrl/SUAS`) must conform to released specifications. Undocumented implementation is not canonical.

---

## 1. Mission

SUAS is a **consent-governed veteran support coordination platform**.

**Canonical mission:** Coordinate the shortest safe and consented path between a veteran's current need and an available human or material support resource.

SUAS records needs, consent, assignments, fulfillment, follow-up, and settlement. It does not diagnose, predict crisis, or automate emergency dispatch.

See [PRODUCT.md](PRODUCT.md).

---

## 2. Roles

| Role | Definition |
|---|---|
| Veteran | Primary data subject and consent granter whose need is coordinated |
| Responder | Human coordinator who claims/is assigned a Support Case |
| Organization Administrator | Admin scoped to one Organization |
| Trusted Contact | Invited person with only explicitly consented visibility |
| Service Provider | Person/organization capable of fulfilling a Service Request; API not required |
| SUAS System Administrator | Global system operator, distinct from Organization Administrator |

---

## 3. Canonical loop

```text
SIGNAL → NEED → CONSENT → COORDINATION → FULFILLMENT → FOLLOW-UP → SETTLEMENT
```

These concepts are not interchangeable: Check-In, Support Signal, Support Case, Service Request, Referral, Assignment, Fulfillment Attempt, Fulfillment, Follow-Up, Settlement.

**Support Case** = coordination around a Veteran.  
**Service Request** = one specific requested need.  
One Case may contain multiple Service Requests.

---

## 4. Pilot scope vs architecture capacity

- Controlled pilot: approximately 25–50 veterans
- Geography: Santa Clara County, California
- MVP categories: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`
- Pilot readiness: `NOT_READY`
- Production readiness: `NOT_READY`

**Pilot scope is not an architecture ceiling.** Production design must avoid avoidable migration barriers if adoption grows quickly. See [SCALING.md](SCALING.md).

---

## 5. Production architecture doctrine

SUAS remains a scalable modular monolith:

```text
Clients
  |
  v
Stateless SUAS application tier
  |
  +--> PostgreSQL
  |
  +--> Durable jobs/workers
          |
          +--> SMS / Email adapters
          +--> Transportation adapter(s)
          +--> Temporary shelter/room adapter(s)
          +--> Food-support adapter(s)
          +--> Peer-support adapter(s)
          `--> Manual coordination adapters
```

Rules:

1. No microservices without measured need and released spec change.
2. Correctness-critical state does not depend on one app process.
3. Production-critical async work is durable across worker restart.
4. External services are capability ports with replaceable adapters.
5. Manual providers are first-class.
6. Provider status never replaces canonical Request/Fulfillment state.
7. External mutations are idempotent and ambiguous outcomes reconcile.
8. Growing APIs are bounded/paginated.
9. Contested Case/Request commands use mutation-time one-winner semantics.
10. Event identity is distinct from command/job idempotency identity.
11. Scale/resilience are verified by load/failure evidence, not assumed.

---

## 6. Referenced MVP visual authority

The existing MVP at `https://suasqrf.org/app/` is the production visual and interaction reference.

Production preserves its action-first hierarchy, low cognitive load, role clarity, resource visibility, responder/QRF immediacy, and mobile-first navigation.

The MVP does not override security, auth, consent, privacy, accessibility, safety, or canonical domain semantics. See [MVP_REFERENCE.md](MVP_REFERENCE.md).

---

## 7. Provider-neutral fulfillment

| Category | Capability port | Provider decision |
|---|---|---|
| `TRANSPORTATION` | `TransportationPort` | D-017 |
| `SHELTER` | `TemporaryShelterPort` | D-018 |
| `FOOD` | `FoodSupportPort` | D-019 |
| `PEER_SUPPORT` | `PeerSupportPort` | D-020 if external; internal/manual valid |

Specific rides, rooms, food, and peer-support vendors are adapters/configuration, not architecture.

Integration modes may include `API`, `WEBHOOK`, `DEEP_LINK`, `PHONE`, `EMAIL`, `MANUAL_COORDINATION`, `NONE`.

---

## 8. Readiness gates

All are `NOT_READY`:

`AUTH`, `CONSENT`, `CHECK-IN`, `COORDINATION`, `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SAFETY`, `PRIVACY`, `SCALE`, `RESILIENCE`, `OPERATIONS`, `REPORTING`.

See [STATUS.md](STATUS.md) and [TESTING.md](TESTING.md).

---

## 9. Governance / review frontier

Preflight work may repair draft contradictions and prepare later owner worksheets, but cannot bypass dependencies or change lifecycle.

| Stage | Current state | Worksheet |
|---|---|---|
| SPEC-001 | `READY_FOR_REVIEW` | [SPEC-001.md](SPEC-001.md) |
| SPEC-002 | blocked by SPEC-001; preflight complete | [SPEC-002.md](SPEC-002.md) |
| SPEC-003 | blocked by SPEC-001/002; preflight complete | [SPEC-003.md](SPEC-003.md) |
| SPEC-004 | blocked by SPEC-001/002; preflight complete | [SPEC-004.md](SPEC-004.md) |
| SPEC-005 | blocked by SPEC-004; preflight complete | [SPEC-005.md](SPEC-005.md) |
| SPEC-006+ | follow [ROADMAP.md](ROADMAP.md) | — |

Only the owner may accept/release artifacts. The first implementation-authoritative release is SPEC-016.

---

## 10. Document index

### Product / authority

- [PRODUCT.md](PRODUCT.md) — mission, roles, categories, loop, non-goals
- [MVP_REFERENCE.md](MVP_REFERENCE.md) — visual/interaction conformance
- [GLOSSARY.md](GLOSSARY.md) — terminology authority
- [STATUS.md](STATUS.md) — governance/readiness state
- [VERSIONING.md](VERSIONING.md) — lifecycle/versioning
- [ROADMAP.md](ROADMAP.md) — ordered specification path
- [DECISIONS.md](DECISIONS.md) — open decisions
- [AGENTS.md](AGENTS.md) — agent/implementation governance

### Architecture / integration / scale

- [ARCHITECTURE.md](ARCHITECTURE.md)
- [DOMAIN_MODEL.md](DOMAIN_MODEL.md)
- [DATA_MODEL.md](DATA_MODEL.md)
- [EVENT_MODEL.md](EVENT_MODEL.md)
- [API.md](API.md)
- [APIS.md](APIS.md)
- [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md)
- [SCALING.md](SCALING.md)
- [RESILIENCE.md](RESILIENCE.md)

### Domain

[AUTH.md](AUTH.md), [CONSENT.md](CONSENT.md), [CHECKINS.md](CHECKINS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [SAFETY.md](SAFETY.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [RESOURCES.md](RESOURCES.md), [REFERRALS.md](REFERRALS.md), [FULFILLMENT.md](FULFILLMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [SETTLEMENT.md](SETTLEMENT.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md).

### Security / operations / verification

[SECURITY.md](SECURITY.md), [PRIVACY.md](PRIVACY.md), [COMPLIANCE.md](COMPLIANCE.md), [ONBOARDING.md](ONBOARDING.md), [ADMIN.md](ADMIN.md), [PILOT.md](PILOT.md), [ANALYTICS.md](ANALYTICS.md), [TESTING.md](TESTING.md), [DEPLOYMENT.md](DEPLOYMENT.md), [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md).

### Owner review worksheets

[SPEC-001.md](SPEC-001.md), [SPEC-002.md](SPEC-002.md), [SPEC-003.md](SPEC-003.md), [SPEC-004.md](SPEC-004.md), [SPEC-005.md](SPEC-005.md).

---

## 11. Key unresolved SPEC-006 handoffs

Preflight has made these schema/model obligations explicit:

- Support Signal computation identity + deterministic effective projection;
- event identity/idempotency/correlation/causation representation;
- replay-safe event publication/outbox semantics;
- one-active-case/active-assignment concurrency representation;
- Service Request current assignment/provider history;
- first-class multi-cycle Settlement history;
- deterministic current/latest Settlement projection;
- blocking vs carried-forward Follow-Up representation;
- Follow-Up schedule/version identity;
- FulfillmentAttempt/ServiceFulfillment history/current constraints;
- command idempotency persistence where needed.

Implementation must not choose these silently before SPEC-006 is accepted/released.

---

## 12. Engineer rules

1. Read PRODUCT/GLOSSARY/README plus relevant released specs before implementation.
2. Draft/preflight/accepted artifacts are not shipping authority until release.
3. Cite spec file/section/version/lifecycle in implementation PRs.
4. Do not invent partners, providers, legal status, signal weights, staffing, SLOs, RTO/RPO, or adoption forecasts.
5. No safety-critical generative AI.
6. No provider SDK/vendor status in domain modules.
7. Provider callbacks are evidence, not hidden state authority.
8. Preserve referenced MVP experience unless documented production constraint requires divergence.
9. Run applicable conformance/readiness suites.
10. Do not claim HIPAA compliance while D-006 is open.
11. Do not add production application code to this specs repository.

---

## 13. Epistemic labels

`OBSERVED`, `INFERRED`, `SPECULATIVE`, `NOT_COMPUTABLE`, `DECISION_PENDING`, `FUTURE`.

---

## 14. Quality rules

- explicit, testable, non-redundant, cross-linked;
- canonical terminology;
- MVP identity preserved without unsafe prototype behavior;
- no unsupported clinical/compliance claims;
- no automated emergency dispatch;
- no vendor-domain lock-in;
- no pilot-only correctness assumptions;
- complexity must remove concrete ambiguity or risk.
