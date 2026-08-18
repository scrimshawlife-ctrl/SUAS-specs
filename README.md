# Shut Up and Serve (SUAS) — Specification v0.1

**Product:** Shut Up and Serve  
**System:** SUAS  
**Version:** `0.1.0`  
**Lifecycle:** `released`  
**Phase:** `IMPLEMENTATION_AUTHORIZED`  
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`  
**Release manifest:** [RELEASE_MANIFEST-0.1.0.md](RELEASE_MANIFEST-0.1.0.md)

`SUAS-specs` is the canonical specification authority. `scrimshawlife-ctrl/SUAS` may now implement the artifacts named in the v0.1.0 release manifest. Code, prototypes, provider behavior, deployment state, or traction still do not silently redefine canon.

---

## 1. Mission / boundaries

**Mission:** Coordinate the shortest safe and consented path between a veteran's current need and an available human or material support resource.

```text
SIGNAL → NEED → CONSENT → COORDINATION → FULFILLMENT → FOLLOW-UP → SETTLEMENT
```

MVP categories: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.

SUAS is not an EHR, diagnosis system, suicide-prediction product, automated emergency dispatcher, or MVP billing platform.

---

## 2. Release status

Owner `@scrimshawlife-ctrl` accepted SPEC-001 through SPEC-015 and released SPEC-016 on 2026-08-18 PT.

- SPEC-001–SPEC-015: `accepted`.
- SPEC-016: `released`.
- Current next stage: SPEC-017 implementation conformance.
- Pilot readiness: `NOT_READY`.
- Production readiness: `NOT_READY`.
- All 12 readiness gates remain `NOT_READY` until reproducible evidence exists.

Release authorizes implementation only. It does **not** authorize production deployment, real veteran data, or a live pilot.

---

## 3. Controlled pilot vs scale

The controlled Santa Clara County pilot remains approximately 25–50 enrolled veterans unless explicitly changed. High demand/traction may justify waitlist or later launch planning, but does not silently expand PilotEnrollment.

Pilot size is an operating boundary, not a technical capacity ceiling. Release-specific production workload/SLO evidence remains deferred under D-021/D-023 because production operation is unavailable in v0.1.0.

---

## 4. Architecture doctrine

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

Released rules:

- scalable modular monolith; no premature microservices;
- correctness-critical state is shared/persistent, not process-local;
- production-critical async work is durable by contract;
- persistent command idempotency is distinct from event identity;
- required Domain Event publication is replay-safe;
- contested Case/assignment/Settlement operations have deterministic one-winner semantics;
- Support Signal/current Settlement/current assignment projections are deterministic;
- Follow-Up stale jobs are version-guarded;
- growing APIs/queries are bounded;
- scale/resilience are proven by evidence, not diagrams.

---

## 5. MVP visual authority

The existing MVP at `https://suasqrf.org/app/` is the visual/interaction reference. [MVP_REFERENCE.md](MVP_REFERENCE.md) preserves the recognizable `TAKE ACTION`, `I NEED SUPPORT`, `I WANT TO SERVE`, QRF deploy/search/contact flow, Immediate Resources, resource browsing, responder on-duty dashboard, Quick Resource Share, Alerts/Chat/Home, and distinct admin surface.

Released truthful divergences include:

- replace contradictory `No email` enrollment copy;
- do not guarantee responder proximity/immediate notification without evidence;
- do not require continuous GPS to preserve prototype wording;
- official crisis copy remains unavailable until D-012 closes;
- prototype statistics/clinical claims are not inherited;
- future category cards may remain informational/`COMING_SOON`, not hidden released workflows.

---

## 6. Provider-neutral fulfillment

Canonical capability ports:

- `TransportationPort`
- `TemporaryShelterPort`
- `FoodSupportPort`
- `PeerSupportPort`

Manual coordination is first-class. Provider SDKs/payloads/statuses stay inside adapters. External mutations use stable `FulfillmentAttempt` idempotency; ambiguous outcomes become `PROVIDER_UNKNOWN` and reconcile before duplicate-risk retry.

For v0.1.0, real production external providers are not selected. Transportation, shelter/room, food, and peer support are `MANUAL_ONLY`/fake-test paths as defined in the release manifest.

---

## 7. Release-safe deferrals

The first cut is implementation-authoritative, not production-operating. The following remain unavailable until their decisions/evidence close:

- production hosting/auth/email/SMS/database/job infrastructure;
- production legal/retention/partner/staffing/counsel posture;
- production Support Signal rules;
- official safety/crisis copy;
- real external service adapters;
- production workload/SLO/RTO/RPO targets;
- small/sensitive aggregate-reporting policy.

D-015 and D-016 defaults are accepted. See [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md).

---

## 8. Readiness gates

All are `NOT_READY`:

`AUTH`, `CONSENT`, `CHECK-IN`, `COORDINATION`, `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SAFETY`, `PRIVACY`, `SCALE`, `RESILIENCE`, `OPERATIONS`, `REPORTING`.

See [STATUS.md](STATUS.md), [TESTING.md](TESTING.md).

---

## 9. Governance chain

| Stage | State |
|---|---|
| SPEC-001 through SPEC-015 | `accepted` |
| SPEC-016 | `released` |
| SPEC-017 | `READY_TO_BEGIN` |
| SPEC-018 | blocked by implementation/evidence/production decisions |
| SPEC-019 | future |

Implementation PRs must cite released file/section/version and the v0.1.0 release manifest. Gaps return to specs; code does not redefine canon.

---

## 10. Core spec index

### Product / authority
[PRODUCT.md](PRODUCT.md), [GLOSSARY.md](GLOSSARY.md), [STATUS.md](STATUS.md), [VERSIONING.md](VERSIONING.md), [ROADMAP.md](ROADMAP.md), [DECISIONS.md](DECISIONS.md), [AGENTS.md](AGENTS.md), [CONTRIBUTING.md](CONTRIBUTING.md), [SPEC_AUDIT.md](SPEC_AUDIT.md), [RELEASE_MANIFEST-0.1.0.md](RELEASE_MANIFEST-0.1.0.md), [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md).

### Architecture / API / scale
[ARCHITECTURE.md](ARCHITECTURE.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [EVENT_MODEL.md](EVENT_MODEL.md), [API.md](API.md), [APIS.md](APIS.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md).

### Domain
[AUTH.md](AUTH.md), [CONSENT.md](CONSENT.md), [CHECKINS.md](CHECKINS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [SAFETY.md](SAFETY.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [RESOURCES.md](RESOURCES.md), [REFERRALS.md](REFERRALS.md), [FULFILLMENT.md](FULFILLMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [SETTLEMENT.md](SETTLEMENT.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md).

### Operations / verification
[MVP_REFERENCE.md](MVP_REFERENCE.md), [ADMIN.md](ADMIN.md), [SECURITY.md](SECURITY.md), [PRIVACY.md](PRIVACY.md), [COMPLIANCE.md](COMPLIANCE.md), [ONBOARDING.md](ONBOARDING.md), [PILOT.md](PILOT.md), [ANALYTICS.md](ANALYTICS.md), [TESTING.md](TESTING.md), [DEPLOYMENT.md](DEPLOYMENT.md), [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md).

---

## 11. Next work

Proceed with SPEC-017 implementation conformance in `scrimshawlife-ctrl/SUAS` against release `0.1.0`. No production-unavailable feature may be made operational merely as an implementation default.
