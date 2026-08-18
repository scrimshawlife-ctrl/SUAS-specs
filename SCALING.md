# SCALING.md — Capacity and horizontal scaling contract (SUAS v0.1)

**Status:** `draft` / `0.1.0` / not implementation authority until accepted and released.  
**Related:** [ARCHITECTURE.md](ARCHITECTURE.md), [DATA_MODEL.md](DATA_MODEL.md), [APIS.md](APIS.md), [TESTING.md](TESTING.md), [OPERATIONS.md](OPERATIONS.md), [RESILIENCE.md](RESILIENCE.md), [STATUS.md](STATUS.md)

---

## 1. Purpose

SUAS may begin with a small controlled pilot, but production architecture must not encode avoidable pilot-scale ceilings. This specification defines scale as a design and verification concern without requiring premature microservices, sharding, or multi-region active-active operation.

The governing rule is:

> Pilot scope may be small. Architectural ceilings should not be.

The modular monolith remains the default architecture. Scale is achieved first through stateless horizontal application capacity, durable asynchronous work, efficient PostgreSQL access, bounded queries, backpressure, and measured optimization.

---

## 2. Scale bands

The following bands are **capacity test envelopes, not adoption forecasts or promises**.

| Band | Purpose | Registered-user envelope |
|---|---|---:|
| `PILOT` | Controlled initial operation | 25–50 veterans plus operators |
| `REGIONAL` | Multiple organizations / county-regional adoption | 1,000–25,000 registered users |
| `MULTI_REGION` | Broad multi-organization adoption | 25,000–250,000+ registered users |

A release does not need to prove the maximum band unless its release target says so. It must, however, avoid known architecture choices that make the next band require a domain rewrite.

---

## 3. Workload dimensions

Capacity planning and load testing must report workload dimensions rather than a single “users” number.

At minimum:

- registered veterans;
- active veterans/day;
- registered responders;
- concurrent responder sessions;
- organizations/tenants;
- concurrent authenticated sessions;
- Check-Ins submitted/minute;
- Support Signals computed/minute;
- Support Cases opened/minute;
- Service Requests created/minute;
- responder case-claim attempts/minute;
- notification jobs/minute;
- SMS/email sends/minute;
- provider calls/minute by capability;
- provider webhooks/minute;
- Follow-Up jobs/minute;
- audit/domain events/day;
- queue depth and oldest-job age;
- database growth/day.

Specific numeric SLOs and load targets remain `DECISION_PENDING` until production hosting and operating expectations are accepted.

---

## 4. Stateless application invariant

The production application tier MUST be horizontally scalable.

No correctness-critical state may exist only in application process memory, including:

- authenticated session truth;
- workflow state;
- locks/leases required for correctness;
- durable job state;
- provider fulfillment attempt state;
- idempotency records;
- consent grants;
- rate-limit/accounting state when correctness depends on it.

A request may be served by any healthy application instance.

Adding or removing an application instance must not require data migration or change domain semantics.

---

## 5. Durable asynchronous work

Production-critical async work MUST use a durable queue/job mechanism or an equivalently durable database-backed job contract.

Required properties:

- persistence across worker restart;
- explicit acknowledgment/completion;
- bounded retry policy;
- idempotent handlers;
- visibility into attempts and last error;
- dead-letter/quarantine handling;
- replay/retry by authorized operator;
- queue depth and oldest-job telemetry;
- priority classes where urgent human-support operations must not be starved by low-priority maintenance work.

An in-process-only queue that loses jobs on restart is not production-ready.

No vendor is selected by this requirement.

---

## 6. Concurrency and atomic commands

All commands that allocate exclusive work or mutate state under contention must have an explicit atomicity contract.

Examples include:

- `CLAIM_CASE`;
- Service Request assignment;
- provider fulfillment-attempt creation;
- one-time token verification;
- idempotency-key creation;
- resource capacity decrement where used.

Two concurrent claim attempts for one claimable Case must produce one successful owner and a deterministic conflict for the other. Read-then-write races without compare-and-set/transactional protection are not acceptable.

---

## 7. API scalability rules

Unbounded collections MUST NOT expose unbounded reads.

Production APIs must provide, as applicable:

- cursor-based pagination for growing collections;
- deterministic sort order;
- bounded page size;
- server-enforced maximums;
- filters scoped to tenant and authorization before pagination;
- no client contract requiring complete history download to render a normal screen;
- idempotency on externally consequential POST/command operations;
- request/body size limits;
- rate limiting appropriate to actor and endpoint.

Offset pagination may be used only where datasets are bounded or consistency/latency tradeoffs are accepted.

---

## 8. PostgreSQL scaling rules

PostgreSQL remains the logical system of record until measured evidence justifies a released architecture change.

Required production design practices:

- connection pooling;
- indexed tenant-scoped access paths;
- indexes for queue/status/due-time queries used operationally;
- query plans reviewed for high-volume paths;
- no N+1 access patterns on responder queues/resource lists;
- bounded transactions;
- migrations designed for growing tables;
- aggregate/report work kept off latency-sensitive request paths where possible;
- append-efficient event/audit storage;
- retention/archive strategy once D-007 is decided;
- read replicas considered only from measured read pressure;
- partitioning considered only from measured table/index/maintenance pressure.

Sharding is not an MVP requirement and must not be introduced speculatively.

---

## 9. Tenant fairness

At scale, one Organization must not trivially exhaust shared resources for every other Organization.

The implementation must support controls for:

- tenant-aware rate limiting where appropriate;
- queue visibility by tenant;
- per-tenant provider configuration;
- bounded export/report jobs;
- abuse/automation limits;
- noisy-neighbor detection.

Hard resource reservations or separate tenant databases are not required unless later justified.

Tenant isolation remains a security invariant, not merely a performance feature.

---

## 10. Notification and event bursts

Notification and event-driven workloads must tolerate bursts without synchronous cascade failure.

Rules:

- notification sends are asynchronous;
- bulk/burst generation creates durable work, not one synchronous provider loop;
- provider rate limits produce backoff and queueing, not request-thread blocking;
- consent is rechecked at send time as specified;
- deduplication/idempotency prevents retry storms;
- queue admission/backpressure protects database and external providers;
- operational alerts trigger on queue age/depth thresholds.

---

## 11. Caching

Caching is an optimization, not a correctness dependency unless explicitly specified.

A cache may be added for measured needs such as resource catalogs or read-heavy configuration, but:

- source of truth remains explicit;
- stale behavior is bounded;
- authorization/tenant boundaries are part of cache keys;
- invalidation behavior is documented;
- absence of cache must degrade performance, not correctness.

No cache product is selected in this spec.

---

## 12. Observability for scale

Production must expose at least:

- request rate, latency, and error rate by route class;
- application instance health;
- DB connection utilization;
- slow-query/query-latency metrics;
- queue depth, throughput, retry count, and oldest-job age;
- worker concurrency and saturation;
- case-claim conflict rate;
- notification throughput/delivery outcome;
- provider latency/error/rate-limit status by adapter;
- webhook processing lag;
- audit/domain event throughput;
- tenant-level load indicators suitable for noisy-neighbor investigation.

Correlation identifiers should include, where applicable and safe:

- `request_id`;
- `organization_id`;
- `case_id`;
- `service_request_id`;
- `fulfillment_attempt_id`;
- `provider_adapter_id`.

Do not put unnecessary veteran PII in metrics or logs.

---

## 13. Load-test profiles

Tests must use synthetic data only.

At least three profiles are required before a production readiness claim:

### 13.1 Steady state

Representative mixed traffic for the release target band.

### 13.2 Burst

A short spike in support requests, notifications, responder claims, and provider activity. The system must apply backpressure rather than cascade.

### 13.3 Degraded dependency

Representative traffic while one external provider is slow/rate-limited/unavailable. SUAS core state must remain correct and operator-visible.

Exact concurrency/rate values are set in a release-specific performance plan and must be recorded with test results.

---

## 14. Extraction rule

A module may be extracted from the modular monolith only when evidence shows one or more of:

- independent scaling demand;
- strong fault-isolation need;
- deployment cadence conflict;
- data/security isolation requirement;
- runtime/resource profile materially incompatible with the monolith.

Extraction requires a released spec change, contract preservation, migration plan, observability plan, and rollback path.

Microservices are not the default scalability strategy.

---

## 15. Acceptance gate: SCALE

`SCALE = READY` for a target release band only when:

- application instances are stateless for correctness;
- at least two application instances can serve the same workload without semantic change;
- production-critical async jobs survive worker restart;
- duplicate job delivery does not duplicate external effects;
- contested case claims/assignments are atomic;
- unbounded lists are paginated and bounded;
- DB connection pooling and high-volume indexes are verified;
- burst testing demonstrates backpressure rather than cascading failure;
- queue and provider saturation are observable;
- one tenant's load does not trivially starve all others;
- the release-specific load profile and results are recorded.

Current status: `NOT_READY`.

---

## 16. Non-goals

- Kubernetes as a requirement
- Microservices as a requirement
- Kafka or any named broker as a requirement
- Database sharding before measured need
- Multi-region active-active before measured/accepted need
- Capacity promises based only on registered-user count
