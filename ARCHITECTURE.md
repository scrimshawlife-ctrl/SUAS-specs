# ARCHITECTURE.md — Scalable modular monolith

**Related:** [PRODUCT.md](PRODUCT.md), [MVP_REFERENCE.md](MVP_REFERENCE.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [EVENT_MODEL.md](EVENT_MODEL.md), [API.md](API.md), [APIS.md](APIS.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md), [DEPLOYMENT.md](DEPLOYMENT.md), [SAFETY.md](SAFETY.md), [SETTLEMENT.md](SETTLEMENT.md)

**Status:** `draft` / `0.1.0`. Unsettled technology choices are `DECISION_PENDING`. Do not lock a cloud, queue, cache, communications, or service-fulfillment vendor.

---

## 1. Purpose

Describe the runtime shape of SUAS for a controlled Santa Clara County pilot while avoiding architecture decisions that create unnecessary migration barriers if adoption grows quickly.

The architecture remains a **modular monolith**. Scale is achieved first through stateless application instances, PostgreSQL, durable background work, capability-port adapters, efficient/bounded APIs, backpressure, and observability.

No microservices without demonstrated load, isolation, deployment, or security need recorded in a later released spec change.

Pilot scope may be small. Architectural ceilings should not be.

---

## 2. High-level shape

```text
Veteran / Responder / Admin clients
              |
              v
      Stateless SUAS API tier
      (modular monolith)
              |
       +------+-------+
       |              |
       v              v
   PostgreSQL      Durable Jobs
       |              |
       |              v
       |           Workers
       |              |
       |       +------+------------------+
       |       |      |        |         |
       |       v      v        v         v
       |      SMS   Email   Provider   Scheduled
       |                    Adapters     Work
       |
       v
Domain/Audit Events
```

Responder and admin clients consume the same application modules through [API.md](API.md). They are not separate backends.

The referenced MVP remains the visual/interaction authority subject to production constraints in [MVP_REFERENCE.md](MVP_REFERENCE.md).

---

## 3. Architecture invariants

1. One logical deployable application architecture; multiple stateless instances may run concurrently.
2. One logical PostgreSQL system of record per environment unless a later released spec changes data topology.
3. Module boundaries are code/data-ownership/authorization boundaries, not network boundaries.
4. Correctness-critical state MUST NOT exist only in one application process.
5. Production-critical asynchronous work MUST survive worker/application restart.
6. External services are accessed through capability ports; vendor SDKs are adapter-local.
7. Manual service coordination remains first-class when provider APIs do not exist or are degraded.
8. Every contested state transition is atomic.
9. Every externally consequential retry path is idempotent.
10. Growing API collections are bounded/paginated.
11. Tenant isolation is a security invariant and must survive horizontal scaling.
12. Extraction to services requires measured need + released spec + migration/rollback plan.
13. Cloud/platform/vendor choices remain decisions, not domain architecture.

---

## 4. Clients

| Client | Users | Notes |
|---|---|---|
| Veteran PWA | Veteran | Check-In, consent, request support/status, trusted circle, fulfillment confirmation |
| Responder console | Responder, Organization Administrator | Coordination console, not an EHR |
| Admin console | SUAS System Administrator, scoped Organization Administrator | Global/scoped admin surfaces |

Native mobile apps and push notifications remain `FUTURE` unless separately promoted.

All production clients must conform to [MVP_REFERENCE.md](MVP_REFERENCE.md) for required visual/interaction continuity.

---

## 5. Module catalog

### 5.1 Auth

- Owns authentication challenges, sessions, MFA factors/recovery state.
- Veteran auth is passwordless per [AUTH.md](AUTH.md).
- Responder/admin MFA required.
- Provider implementation behind `AuthPort` where external.
- Sessions/correctness state must work across multiple app instances.

### 5.2 Veteran Profiles

- Owns `VeteranProfile`, `PilotEnrollment`.
- Veteran self-service; scoped responder/admin read as authorized.

### 5.3 Check-ins

- Owns questionnaire versions, Check-Ins, responses.
- Completion commits synchronously; signal computation may be durable async work.

### 5.4 Support Signals

- Owns deterministic, versioned `SupportSignal` computation/storage.
- No generative primary signal.

### 5.5 Consent

- Owns `ConsentGrant`, `ConsentEvent`.
- All disclosure/notify/provider projection paths evaluate consent at use time as specified.

### 5.6 Trusted Circle

- Owns trusted-contact invitation/membership lifecycle.
- Membership alone grants no visibility.

### 5.7 Cases

- Owns `SupportCase`, case assignments/notes/contact history as specified.
- Case claim/assignment commands are atomic under contention.

### 5.8 Requests

- Owns `ServiceRequest`.
- Canonical state machine is independent of provider status.

### 5.9 Dispatch

- Owns documented Service Request transition logic and coordination rules.
- MVP matching remains responder/catalog driven unless later specified.
- External provider routing is not a hidden Service Request state machine.

### 5.10 Resources

- Owns `Resource`, `ResourceCategory`, `ServiceOffer` and provider/resource metadata.
- A Resource may declare integration modes without embedding provider SDK details.

### 5.11 Referrals

- Owns `Referral` records distinct from Service Requests and Fulfillment.

### 5.12 Fulfillment

- Owns `ServiceFulfillment` and SUAS-side Fulfillment Attempt records required for external idempotency/reconciliation.
- Depends on Requests, Consent, Provider Router/ports, Event Layer.
- External provider status never directly replaces canonical Fulfillment semantics.
- See [FULFILLMENT.md](FULFILLMENT.md) and [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md).

### 5.13 Provider Router / Adapters

- **Purpose:** select and invoke configured provider adapters for an accepted capability.
- **Owns:** adapter configuration/health references and routing policy configuration; does not own Service Request state.
- **Depends on:** Resources, Fulfillment, Consent, Operations.
- **Ports:** `TransportationPort`, `TemporaryShelterPort`, `FoodSupportPort`, `PeerSupportPort` plus infrastructure ports in [APIS.md](APIS.md).
- **Adapters:** provider-specific; vendor SDKs/payloads do not escape this boundary.
- **Manual Adapter:** required for each MVP fulfillment capability.
- **Spec:** [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md).

### 5.14 Follow-up

- Owns first-class `FollowUp` work items and durable due/overdue processing.

### 5.15 Notifications

- Owns Notification and preference state.
- EMAIL/SMS/IN_APP MVP; PUSH future.
- Sends are durable async work in production.
- External channels use capability ports.

### 5.16 Administration

- Owns Organizations, memberships, responder/admin configuration, pilot configuration, template publication, scoped provider configuration where authorized.
- Organization Administrator is not SUAS System Administrator.

### 5.17 Audit / Event Layer

- Immutable append-only Domain/Audit Events.
- Domain write and required event append occur atomically where feasible; any asynchronous completion path must be durable and idempotent.
- Event/audit growth is an explicit scaling concern.

---

## 6. Database ownership and tenancy

- PostgreSQL is the logical system of record.
- Module-owned tables remain explicit.
- Tenant-owned rows carry tenant/organization scope as defined by [DATA_MODEL.md](DATA_MODEL.md).
- Authorization is role + tenant + row + consent.
- High-volume tenant/status/due-time queries require indexes appropriate to measured/load-tested access paths.
- Unbounded operational history is not loaded wholesale into normal UI paths.
- Cross-tenant reads must remain blocked across API, jobs, provider callbacks, caches, and reporting.

Sharding is not required for MVP. Read replicas/partitioning/sharding require measured need and a later accepted/released architecture change where applicable. See [SCALING.md](SCALING.md).

---

## 7. Application statelessness

Any healthy app instance must be able to serve any authorized request.

The following cannot be process-local truth:

- session validity;
- Consent Grants;
- Case/Service Request/Fulfillment state;
- durable jobs;
- idempotency records;
- Fulfillment Attempt state;
- correctness-critical locks/leases;
- provider reconciliation state.

Process-local caches may exist only as disposable optimizations.

---

## 8. Durable background jobs

Production-critical jobs include at least:

| Job | Trigger | Requirements |
|---|---|---|
| Compute Support Signal | Check-In completed | Durable; idempotent; persist before event |
| Due Follow-Up scan | Periodic | Durable/observable; tenant scoped |
| Overdue escalation | Periodic/event | Durable; bounded retries |
| Notification send | Notification enqueued | Durable; consent recheck; adapter retry policy |
| Notification retry | Failed attempt | Bounded/backoff; dead-letter visibility |
| Provider fulfillment action | Fulfillment Attempt | Durable; idempotency key; reconcile unknown outcome |
| Provider status reconciliation | ambiguous/delayed outcome | Durable; adapter-local external lookup |
| Provider webhook processing | authenticated webhook | Deduplicated; out-of-order safe |
| Resource freshness report | periodic | Lower priority than live support work |
| Auth/session expiry work | periodic | Idempotent |

A volatile in-process-only queue that loses production-critical work on restart is not production-ready. Exact durable queue/job product is D-022.

---

## 9. Sync vs async

**Synchronous:** authentication verify, consent evaluation, reads, state-transition commit, admin commands whose success must be known immediately.

**Asynchronous:** external notifications, external provider actions when not required to complete the user request transaction, support-signal computation, due/overdue scans, reconciliation, resource freshness.

A user-facing command must not report success for a canonical state transition that has not committed.

An external action may be accepted for processing and expose an explicit pending state where the domain contract permits it.

---

## 10. Concurrency and idempotency

Atomic concurrency is required for operations such as:

- Case claim;
- Service Request assignment;
- Fulfillment Attempt creation;
- one-time challenge verification;
- idempotency-key reservation;
- any resource-capacity mutation later introduced.

Network/client/job retries that can duplicate external consequences must use stable idempotency identity.

Provider timeouts with ambiguous outcome follow [RESILIENCE.md](RESILIENCE.md), not blind retry.

---

## 11. Provider-neutral external services

Infrastructure ports:

- `AuthPort`
- `SmsPort`
- `EmailPort`

Service-fulfillment ports:

- `TransportationPort`
- `TemporaryShelterPort`
- `FoodSupportPort`
- `PeerSupportPort`

Provider Router selects configured adapters by explicit operational policy. Provider names/status/payload types remain adapter-local.

Manual coordination is a valid adapter mode. Do not assume rides, rooms, food, or peer providers expose transactional APIs.

See [APIS.md](APIS.md) and [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md).

---

## 12. Scalability doctrine

Capacity bands in [SCALING.md](SCALING.md) are test envelopes, not adoption forecasts.

The first scaling response should be:

1. measure;
2. remove inefficient queries/work;
3. add stateless app/worker capacity;
4. tune PostgreSQL/indexes/pooling;
5. apply backpressure and adapter concurrency limits;
6. add cache/read replica/partitioning only from evidence;
7. extract a service only when module-specific evidence justifies it.

Do not begin with distributed-system complexity.

---

## 13. Resilience doctrine

External dependencies fail independently.

Required properties include:

- finite timeouts;
- bounded/backoff retries;
- provider rate-limit handling;
- circuit breaking;
- durable failed-work visibility;
- duplicate job/webhook safety;
- ambiguous external-outcome reconciliation;
- manual fallback where policy permits;
- backpressure under bursts;
- backup/restore testing.

See [RESILIENCE.md](RESILIENCE.md).

---

## 14. Observability

Production telemetry must support investigation of:

- API rate/latency/errors;
- DB pool/query health;
- queue depth/age/throughput/retries;
- worker saturation;
- notification delivery;
- provider latency/errors/rate limits/circuit state;
- webhook lag;
- case-claim conflicts;
- ambiguous/reconciled fulfillment attempts;
- audit/event growth;
- noisy-neighbor tenant behavior.

Use correlation ids without unnecessary veteran PII.

---

## 15. Security and authorization

Authentication alone is not authorization.

Authorization remains role + tenant + row + consent. Provider disclosure also applies minimum-necessary field projection.

Adapter webhooks are authenticated and cannot authorize a new disclosure or bypass canonical transitions.

See [AUTH.md](AUTH.md), [CONSENT.md](CONSENT.md), [SECURITY.md](SECURITY.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md).

---

## 16. AI policy

No generative AI for safety-critical decisions including primary Support Signal, clinical/suicidality determination, emergency intervention, Trusted Contact notification decision, service qualification, or case closure.

Future assistive uses require later specs and human control.

---

## 17. Funding / billing boundary

```text
Fulfillment -> Funding Eligibility -> Funding Source -> Optional Billing Adapter
```

Status remains `FUTURE`. No MVP payment-card/checkout architecture. Do not assert Medi-Cal billability.

---

## 18. Future extraction

A module may be extracted only after measured evidence of independent scaling, fault isolation, deployment cadence, security/data isolation, or incompatible runtime needs.

Extraction requires:

- released spec change;
- preserved API/domain semantics;
- data-ownership plan;
- migration/rollback plan;
- observability plan;
- failure/degradation contract.

---

## 19. Unsettled technology

| Topic | Status |
|---|---|
| Cloud provider | D-001 `DECISION_PENDING` |
| Auth provider | D-002 `DECISION_PENDING` |
| SMS provider | D-003 `DECISION_PENDING` |
| Email provider | D-004 `DECISION_PENDING` |
| Database hosting | D-005 `DECISION_PENDING` |
| PWA framework | `DECISION_PENDING` |
| Durable job/queue product | D-022 `DECISION_PENDING` |
| Transportation adapter(s) | D-017 `DECISION_PENDING` |
| Temporary shelter/room adapter(s) | D-018 `DECISION_PENDING` |
| Food-support adapter(s) | D-019 `DECISION_PENDING` |
| External peer-support adapter | D-020 `DECISION_PENDING` if used |
| Cache product | Optional / measured need; not selected |
| First-release scale target | D-021 `DECISION_PENDING` |
| Performance SLO thresholds | D-023 `DECISION_PENDING` |
| RTO/RPO | D-024 `DECISION_PENDING` |

---

## 20. Non-goals

- Microservices as default
- Kubernetes as requirement
- Event sourcing as system of record
- Database sharding before evidence
- Multi-region active-active as an MVP requirement
- Vendor-specific domain models
- Provider APIs as a prerequisite for resource validity
- VA/county/Medi-Cal integration assumptions
