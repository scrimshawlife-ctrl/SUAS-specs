# Shut Up and Serve (SUAS) — Specification v0.1

**Product:** Shut Up and Serve  
**System:** SUAS  
**Version:** `0.1.0`  
**Lifecycle:** `draft`  
**Phase:** `SPECIFICATION_BOOTSTRAP`  
**Implementation authority:** `NOT_YET_RELEASED`

`SUAS-specs` is the canonical specification authority. `scrimshawlife-ctrl/SUAS` implements only released contracts. Prototype, code, deployment state, provider behavior, or traction do not silently redefine canon.

---

## 1. Mission / boundaries

**Mission:** Coordinate the shortest safe and consented path between a veteran's current need and an available human or material support resource.

```text
SIGNAL → NEED → CONSENT → COORDINATION → FULFILLMENT → FOLLOW-UP → SETTLEMENT
```

MVP categories: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.

SUAS is not an EHR, diagnosis system, suicide-prediction product, automated emergency dispatcher, or MVP billing platform.

---

## 2. Controlled pilot vs scale

The controlled Santa Clara County pilot remains approximately 25–50 enrolled veterans unless explicitly changed. High demand/traction may justify waitlist or later launch planning, but does not silently expand PilotEnrollment.

Pilot size is an operating boundary, not a technical capacity ceiling. D-021/D-023 define release-specific workload/performance evidence; unsupported regional/multi-region user-count forecasts are not canonical.

---

## 3. Production architecture doctrine

```text
Veteran / Responder / Admin clients
              |
              v
     Stateless SUAS application
              |
       +------+------+
       |             |
       v             v
  PostgreSQL     Durable Jobs
                     |
          +----------+----------+
          |          |          |
     Notifications Provider   Scheduled/
                   Adapters   Reconcile
```

Core rules:

- scalable modular monolith; no premature microservices;
- correctness-critical state is shared/persistent, not process-local;
- production-critical async work is durable;
- persistent command idempotency is distinct from event identity;
- required Domain Event publication is replay-safe;
- contested Case/assignment/Settlement operations have deterministic one-winner semantics;
- Support Signal/current Settlement/current assignment projections are deterministic;
- Follow-Up stale jobs are version-guarded;
- growing APIs/queries are bounded;
- scale and resilience are proven by evidence, not diagrams.

---

## 4. MVP visual authority

The existing MVP at `https://suasqrf.org/app/` is the visual/interaction reference. [MVP_REFERENCE.md](MVP_REFERENCE.md) preserves the recognizable `TAKE ACTION`, `I NEED SUPPORT`, `I WANT TO SERVE`, QRF deploy/search/contact flow, immediate resources, resource category browsing, responder on-duty dashboard, Quick Resource Share, Alerts/Chat/Home, and distinct admin surface.

Production must be truthful where the prototype is not canonical:

- replace contradictory `No email` enrollment copy;
- do not guarantee responder proximity/immediate notification without evidence;
- do not require continuous GPS to preserve “near you” wording;
- exact crisis copy follows accepted SAFETY/D-012;
- prototype statistics/clinical claims are not inherited;
- future category cards may remain informational/`COMING_SOON`, not hidden released workflows.

`UI_CONFORMANCE` is a readiness gate.

---

## 5. Provider-neutral fulfillment

Canonical capability ports:

- `TransportationPort`
- `TemporaryShelterPort`
- `FoodSupportPort`
- `PeerSupportPort`

Integration modes include `API`, `WEBHOOK`, `DEEP_LINK`, `PHONE`, `EMAIL`, `MANUAL_COORDINATION`, `NONE`.

Manual coordination is first-class. A Service Provider does not need an API.

Provider SDKs/payloads/statuses stay inside adapters. External mutations use stable `FulfillmentAttempt` idempotency. Ambiguous outcomes become `PROVIDER_UNKNOWN` and reconcile before duplicate-risk retry.

See [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md).

---

## 6. Readiness gates

All are `NOT_READY`:

`AUTH`, `CONSENT`, `CHECK-IN`, `COORDINATION`, `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SAFETY`, `PRIVACY`, `SCALE`, `RESILIENCE`, `OPERATIONS`, `REPORTING`.

See [STATUS.md](STATUS.md), [TESTING.md](TESTING.md).

---

## 7. Governance / owner review chain

Preflight repairs draft contradictions and prepares review artifacts. **Preflight is not acceptance.** Only the owner changes lifecycle.

| Stage | Purpose | State |
|---|---|---|
| [SPEC-001](SPEC-001.md) | product/authority | `READY_FOR_REVIEW` |
| [SPEC-002](SPEC-002.md) | consent/privacy/safety/security | blocked; preflight complete |
| [SPEC-003](SPEC-003.md) | Check-In/signal/events | blocked; preflight complete |
| [SPEC-004](SPEC-004.md) | Case/Request/responder workflow | blocked; preflight complete |
| [SPEC-005](SPEC-005.md) | resources/referrals/fulfillment/follow-up/settlement | blocked; preflight complete |
| [SPEC-006](SPEC-006.md) | domain/data/event/architecture reconciliation | blocked; preflight complete |
| [SPEC-007](SPEC-007.md) | API/auth/notifications/admin | blocked; preflight complete |
| [SPEC-008](SPEC-008.md) | MVP visual conformance | blocked; preflight complete |
| [SPEC-009](SPEC-009.md) | provider-neutral fulfillment | blocked; preflight complete |
| [SPEC-010](SPEC-010.md) | scaling | blocked; preflight complete |
| [SPEC-011](SPEC-011.md) | resilience | blocked; preflight complete |
| [SPEC-012](SPEC-012.md) | testing/readiness evidence | blocked; preflight complete |
| [SPEC-013](SPEC-013.md) | deployment/operations/incidents/recovery | blocked; preflight complete |
| [SPEC-014](SPEC-014.md) | controlled pilot/analytics | blocked; preflight complete |
| [SPEC-015](SPEC-015.md) | release decisions/safe deferrals | blocked; preflight complete |
| [SPEC-016](SPEC-016.md) | first release assembly | blocked by SPEC-001–015 |
| SPEC-017 | implementation conformance | post-release |
| SPEC-018 | launch readiness | post-conformance |
| SPEC-019 | post-launch revision | future |

First implementation-authoritative release is SPEC-016. Release is not launch readiness.

---

## 8. Open decisions

D-001 through D-025 are canonical in [DECISIONS.md](DECISIONS.md).

Key hardening decisions:

- D-017–D-020 external service adapters;
- D-021 release workload/capacity envelope;
- D-022 durable job/queue implementation;
- D-023 performance SLOs/alerts;
- D-024 RTO/RPO;
- D-025 aggregate reporting privacy/small-cell policy.

No provider, infrastructure product, capacity/SLO/recovery number, or reporting threshold may be guessed.

SPEC-015 classifies each release-relevant decision as `MUST CLOSE`, conditional, or safely deferrable only with an explicit feature boundary.

---

## 9. Core spec index

### Product / authority
[PRODUCT.md](PRODUCT.md), [GLOSSARY.md](GLOSSARY.md), [STATUS.md](STATUS.md), [VERSIONING.md](VERSIONING.md), [ROADMAP.md](ROADMAP.md), [DECISIONS.md](DECISIONS.md), [AGENTS.md](AGENTS.md), [CONTRIBUTING.md](CONTRIBUTING.md), [SPEC_AUDIT.md](SPEC_AUDIT.md).

### Architecture / API / scale
[ARCHITECTURE.md](ARCHITECTURE.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [EVENT_MODEL.md](EVENT_MODEL.md), [API.md](API.md), [APIS.md](APIS.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md).

### Domain
[AUTH.md](AUTH.md), [CONSENT.md](CONSENT.md), [CHECKINS.md](CHECKINS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [SAFETY.md](SAFETY.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [RESOURCES.md](RESOURCES.md), [REFERRALS.md](REFERRALS.md), [FULFILLMENT.md](FULFILLMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [SETTLEMENT.md](SETTLEMENT.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md).

### Operations / verification
[MVP_REFERENCE.md](MVP_REFERENCE.md), [ADMIN.md](ADMIN.md), [SECURITY.md](SECURITY.md), [PRIVACY.md](PRIVACY.md), [COMPLIANCE.md](COMPLIANCE.md), [ONBOARDING.md](ONBOARDING.md), [PILOT.md](PILOT.md), [ANALYTICS.md](ANALYTICS.md), [TESTING.md](TESTING.md), [DEPLOYMENT.md](DEPLOYMENT.md), [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md).

---

## 10. Production correctness additions

The preflight now includes first-class logical representations for:

- auth challenges and revocable sessions;
- Support Signal computation/effective projection;
- command idempotency;
- event correlation/causation/replay-safe publication;
- `ProviderAdapterConfiguration`;
- `FulfillmentAttempt`;
- Follow-Up schedule identity/business retry count/blocking disposition;
- multi-cycle `Settlement` history/current projection;
- Notification logical-send dedupe.

These remain unaccepted draft contracts until their roadmap stages are owner-reviewed.

---

## 11. Engineer rules

- Implement against **released** specs only.
- Cite spec file/section/version/lifecycle/artifact pins.
- Return gaps to specs; do not invent implementation defaults.
- Never encode provider brands/SDK statuses into domain semantics.
- Preserve MVP experience except for documented truthful/safe production divergences.
- No safety-critical generative AI or automated emergency dispatch.
- No unsupported HIPAA/clinical/causal claims.
- No invented capacity/recovery/reporting thresholds.

---

## 12. Release boundary

[SPEC-016.md](SPEC-016.md) requires prerequisite acceptance, a D-001–D-025 release decision ledger, pinned artifact versions, feature availability manifest, and cross-artifact consistency check before any named artifact becomes `released`.

Until the owner executes that release:

`IMPLEMENTATION_AUTHORITY = NOT_YET_RELEASED`.
