# SCALING.md — Capacity and horizontal scaling contract (SUAS v0.1)

**Status:** `draft` / `0.1.0`; SPEC-010 dependency-blocked.  
**Related:** [ARCHITECTURE.md](ARCHITECTURE.md), [DATA_MODEL.md](DATA_MODEL.md), [APIS.md](APIS.md), [TESTING.md](TESTING.md), [OPERATIONS.md](OPERATIONS.md), [RESILIENCE.md](RESILIENCE.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md)

---

## 1. Purpose

SUAS begins with a controlled pilot but must avoid architecture choices that create unnecessary migration barriers if adoption grows quickly.

> Pilot scope may be small. Architectural ceilings should not be.

The modular monolith remains the default. Scale first through stateless app capacity, durable async work, efficient PostgreSQL access, bounded queries, backpressure, tenant fairness, and measured optimization.

---

## 2. Capacity envelopes

The only currently specified operating population is the controlled pilot in [PILOT.md](PILOT.md).

No numeric regional/multi-region production capacity is canonical yet.

**D-021 = release-specific target capacity envelope** and remains `DECISION_PENDING`.

A release performance plan must define its target using workload dimensions in §3, not a vague registered-user count alone.

Architecture may use qualitative planning stages such as:

| Planning stage | Meaning |
|---|---|
| `PILOT` | current controlled launch scope specified in PILOT.md |
| `MULTI_ORG_GROWTH` | multiple organizations/greater concurrent operational load; numeric envelope must be set by D-021 |
| `BROADER_EXPANSION` | later geography/organization growth; numeric envelope remains release-specific |

These labels are not adoption forecasts, contractual capacity promises, or permission to invent numeric targets.

---

## 3. Workload dimensions

Every capacity plan/load test reports, as applicable:

- registered/active veterans;
- registered/active responders;
- organizations/tenants;
- concurrent authenticated/responder sessions;
- Check-Ins submitted per time unit;
- Support Signals computed per time unit;
- Cases/Service Requests created per time unit;
- contested Case claim attempts;
- Notification logical sends and provider attempts per time unit;
- SMS/email sends per time unit;
- provider calls/webhooks by capability;
- Follow-Up due/overdue jobs;
- Domain/Audit Events generated;
- queue depth, throughput, oldest-job age;
- DB connections/query rate/slow-query distribution;
- storage/database growth;
- tenant concentration/noisy-neighbor distribution.

Exact values belong to D-021/D-023 release planning and test evidence.

---

## 4. Stateless application invariant

No correctness-critical state may live only in one app process, including session/revocation truth, workflow state, locks/leases, durable jobs, provider attempts/reconciliation, command idempotency, consent, or correctness-critical rate-limit state.

Any healthy app instance may serve any authorized request. Adding/removing an instance cannot change domain semantics or require data migration.

---

## 5. Durable asynchronous work

Production-critical async work uses durable queue/job semantics or equivalently durable DB-backed work.

Required properties:
- persistence across restart;
- explicit ack/completion;
- bounded retry/backoff;
- idempotent handlers;
- failed-work/dead-letter visibility;
- authorized replay;
- queue depth/oldest-age telemetry;
- priority/admission controls so low-priority maintenance cannot starve urgent support work.

Volatile in-process-only work is not production-ready. Exact product is D-022.

---

## 6. Concurrency / atomic commands

Contested/exclusive operations have explicit one-winner semantics, including:
- one-active Case creation where policy requires it;
- `CLAIM_CASE` / assignment / reassignment;
- Service Request assignment;
- FulfillmentAttempt creation;
- one-time auth challenge consumption;
- command idempotency reservation;
- Settlement resolution-cycle creation/current projection update;
- Follow-Up reschedule vs stale due job;
- future resource-capacity mutation if introduced.

Read-then-write without transactional/CAS/constraint protection is insufficient.

---

## 7. API scaling rules

Growing collections require stable cursor/keyset-style pagination, deterministic ordering, bounded page sizes/server maximums, tenant/auth filtering before pagination, body limits, and endpoint/actor rate controls.

Normal screens must not require complete-history downloads. Unsafe commands use persistent idempotency.

---

## 8. PostgreSQL scaling doctrine

PostgreSQL remains logical system of record until measured evidence justifies a released change.

Required practices:
- connection pooling;
- indexed tenant/current-status/current-projection paths;
- efficient queue/due/reconciliation/idempotency lookup;
- query-plan review for high-volume paths;
- no N+1 responder/resource screens;
- bounded transactions;
- migration planning for growing tables;
- aggregate/report work away from latency-sensitive paths when possible;
- append-efficient events/audit;
- retention/archive strategy after D-007;
- read replicas/partitioning/sharding only from measured evidence.

---

## 9. Tenant fairness

One Organization must not trivially exhaust shared capacity for all others.

System must support tenant-aware rate/admission controls where appropriate, bounded exports/reports, per-tenant provider configuration, noisy-neighbor detection, and queue visibility by tenant while preserving tenant isolation.

Separate tenant databases/hard reservations are not required absent evidence.

---

## 10. Burst / backpressure behavior

Notification, QRF/support-request, event, and provider bursts become durable work rather than synchronous provider loops.

Required:
- queue admission/backpressure protects DB/providers;
- provider rate limits create backoff, not request-thread blocking;
- consent is rechecked at send/disclosure time;
- idempotency/dedupe prevents retry storms;
- urgent support work has higher operational priority than maintenance/freshness jobs;
- overload may degrade non-critical features before corrupting/losing support work.

---

## 11. Caching

Cache is optional optimization, not correctness authority. Source of truth, tenant/auth cache keys, staleness bounds, and invalidation must be explicit. Cache absence may reduce performance but not correctness.

---

## 12. Observability

At minimum measure:
- request rate/latency/errors by route class;
- app instance health;
- DB pool/query health;
- queue depth/age/throughput/retries/dead letters;
- worker saturation;
- case-claim/idempotency conflict rates;
- notification throughput/outcomes/dedupe;
- provider latency/errors/rate limits/circuit/reconciliation backlog;
- webhook lag;
- signal dedupe/event-outbox lag;
- Settlement resolution-cycle conflicts;
- tenant load/noisy-neighbor indicators.

Correlation ids avoid unnecessary veteran PII.

---

## 13. Release-specific load profiles

Synthetic data only.

Every target release performance plan includes at least:

### Steady state
Representative mixed traffic for D-021 workload dimensions.

### Burst
Short support-request/QRF/notification/claim/provider spike. System applies backpressure and preserves correctness.

### Degraded dependency
Representative load while an external provider/communications dependency is slow, rate-limited, or unavailable.

### Concurrency correctness
Focused contested Case claims, idempotent command retries, duplicate jobs/webhooks, Settlement resolve retries, and stale Follow-Up jobs under multi-instance execution.

Exact rates/concurrency/latency targets are D-021/D-023 and must be recorded with results rather than invented in this spec.

---

## 14. Extraction rule

Module extraction requires evidence of independent scaling, fault-isolation, deployment, data/security, or runtime-resource need plus released spec, migration/rollback, observability, and contract preservation.

Microservices are not the default scalability strategy.

---

## 15. SCALE gate

`SCALE = READY` for a target release only when:

- D-021 target workload envelope is recorded;
- D-023 relevant performance SLO/alerts are recorded;
- at least two app instances serve the same workload without semantic change;
- sessions/revocation/idempotency work across instances;
- production-critical jobs survive worker restart;
- duplicate jobs/requests do not duplicate business/external effects;
- contested Case/assignment/Settlement operations are atomic;
- growing lists are bounded;
- DB pooling/access paths are verified;
- burst testing demonstrates backpressure instead of cascading failure;
- provider/queue saturation is observable;
- tenant fairness/noisy-neighbor controls are demonstrated for the release envelope;
- test artifacts record workload dimensions, environment, results, and caveats.

Current: `NOT_READY`.

---

## 16. Non-goals

- unsupported numeric adoption/capacity forecasts;
- Kubernetes requirement;
- microservices requirement;
- named broker/cache requirement;
- speculative sharding;
- multi-region active-active before measured/accepted need;
- capacity claims based only on registered-user count.
