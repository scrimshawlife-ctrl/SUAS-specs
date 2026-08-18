# TESTING.md — Test suites and production-readiness gates (SUAS v0.1)

**Related:** [STATUS.md](STATUS.md), [MVP_REFERENCE.md](MVP_REFERENCE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md), [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [FULFILLMENT.md](FULFILLMENT.md), [CONSENT.md](CONSENT.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [SAFETY.md](SAFETY.md), [SECURITY.md](SECURITY.md)

**Status:** `draft` / `0.1.0`.

---

## 1. Purpose

Define the critical suites and acceptance gates that must pass before a domain, MVP, or production release is claimed ready.

Draft specs are not implementation authority. Tests in `SUAS` must cite the released spec sections they encode.

---

## 2. Test layers

| Layer | What it proves |
|---|---|
| Unit | Pure functions, validators, signal compute, freshness, adapter mapping |
| Domain/state-machine | Only documented Case/Service Request/Fulfillment transitions |
| Authorization | Role × tenant × row × consent matrix |
| Tenant isolation | Org A cannot read/affect Org B through API, jobs, adapters, reports |
| Integration | Module boundaries + PostgreSQL + durable work mechanism |
| API contract | [API.md](API.md) paths, errors, idempotency, pagination |
| Provider adapter conformance | Capability-port behavior independent of provider vendor |
| Notification | Enqueue/send/retry/revoke-before-send/degraded channels |
| Security | Threat categories and webhook auth |
| Migration | Schema migration on empty and representative fixture-filled DB |
| End-to-end | Veteran PWA + responder/admin surfaces against TEST/STAGING |
| Visual regression | Required MVP-derived surfaces and responsive hierarchy |
| Accessibility | WCAG 2.2 AA checks + keyboard/mobile critical paths |
| Load/performance | Steady, burst, degraded-dependency profiles |
| Resilience/failure drill | Restart, timeout, duplicate, queue backlog, DB/provider failure |
| Pilot simulation | Synthetic end-to-end support loop; no production veteran data |

---

## 3. Critical domain/security suites

| Suite | Spec | Must prove |
|---|---|---|
| Support-signal determinism | [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md) | Same inputs + versions → same level/basis; no historical mutation |
| Consent revocation | [CONSENT.md](CONSENT.md) | Revocation stops future view/notify/refer/provider disclosure; history preserved |
| Trusted-circle visibility | [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md) | Membership without matching grant sees nothing |
| Cross-tenant isolation | [SECURITY.md](SECURITY.md) | No leakage through API, jobs, caches, adapters, reporting |
| Service-request transitions | [DISPATCH.md](DISPATCH.md) | Illegal edges fail; assignment ≠ fulfillment |
| Case transitions | [CASES.md](CASES.md) | Illegal edges fail; resolve requires Settlement |
| Notification consent | [NOTIFICATIONS.md](NOTIFICATIONS.md) | Grant rechecked before send |
| Red-state behavior | [SAFETY.md](SAFETY.md) | Approved resources/human review; no automated emergency dispatch/diagnosis |
| Stale-resource handling | [RESOURCES.md](RESOURCES.md) | Inactive/stale behavior explicit and safe |
| Responder authorization | [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md) | Actions fail without role/assignment |
| Contact log | [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [API.md](API.md) | Required fields/events; Case Note is not contact log |
| Veteran visibility | [CASES.md](CASES.md) | No Case Notes/contact attempts/other veteran/org leakage |
| Notification attempts | [NOTIFICATIONS.md](NOTIFICATIONS.md) | One logical Notification row; immutable Audit Event per attempt |
| Audit-event immutability | [EVENT_MODEL.md](EVENT_MODEL.md) | Application roles cannot update/delete events |

A PR that touches a domain without updating/running the matching suite is incomplete.

---

## 4. Provider-adapter conformance suite

Every enabled service provider adapter must pass the same capability contract.

Required tests:

1. domain package imports no provider SDK;
2. adapter maps vendor payloads into SUAS-owned normalized shapes;
3. two fake adapters can be swapped without domain changes;
4. `ManualAdapter` path works for each MVP service capability;
5. duplicate delivery/retry with one Fulfillment Attempt idempotency key produces one logical external mutation;
6. deliberate provider switch creates a new Fulfillment Attempt rather than reusing the old attempt;
7. ambiguous timeout becomes `PROVIDER_UNKNOWN` and reconciles before dangerous retry;
8. duplicate/out-of-order webhook is safe;
9. unauthenticated webhook is rejected;
10. provider status cannot bypass canonical Service Request/Fulfillment transitions;
11. minimum-necessary provider data projection is enforced;
12. provider outage preserves the Service Request and permits configured fallback/manual coordination;
13. rate-limit/backoff/circuit behavior is observable;
14. provider-specific error/status strings do not leak into canonical enum contracts.

Provider selection D-017–D-020 does not change this suite.

---

## 5. UI and MVP-reference conformance suite

Visual tests must use deterministic synthetic fixtures.

Required surfaces are defined in [MVP_REFERENCE.md](MVP_REFERENCE.md), including:

- action/landing;
- veteran enrollment;
- request-support flow;
- resource categories;
- resource list/detail;
- responder/QRF home;
- responder availability;
- active needs/alerts;
- chat/communication;
- admin overview;
- mobile navigation.

Each visual fixture records:

- role;
- viewport;
- fixture dataset/version;
- reference revision/date;
- conformance class (`MUST_MATCH`, `MUST_PRESERVE_BEHAVIOR`, `MAY_EVOLVE`, `MUST_CHANGE_FOR_PRODUCTION`).

Pass condition is not pixel equality. Tests/review must detect missing primary actions, hierarchy drift, increased navigation depth, unreadable density, responsive breakage, category drift, or undocumented production divergence.

Accessibility checks include keyboard operation for responder/admin surfaces, visible focus, semantic labels, color-independent signal meaning, mobile touch targets, reflow/text zoom, and WCAG 2.2 AA target conformance.

---

## 6. Scale/performance suite

Performance plans use synthetic data and report workload dimensions from [SCALING.md](SCALING.md), not only registered-user count.

Before a production readiness claim, run at least:

### 6.1 Steady-state profile

Representative mixed workload for the release target band (D-021).

### 6.2 Burst profile

Short spike in support requests, responder claim attempts, notifications, provider jobs, and webhooks.

Pass behavior:

- system remains correct;
- backpressure is visible;
- queues may grow but do not silently lose work;
- database/provider saturation does not cascade into hidden corruption;
- critical/priority work is not starved by maintenance/bulk tasks.

### 6.3 Degraded dependency profile

Representative traffic while one provider/channel is slow, rate-limited, or unavailable.

Pass behavior:

- SUAS core workflow remains available/correct;
- dependency health is observable;
- provider work queues/backoff rather than stampeding;
- manual/alternate route works where configured.

### 6.4 Horizontal application profile

At least two application instances serve the same environment/workload.

Pass behavior:

- session/domain correctness unaffected by which instance handles a request;
- no process-local lock/state assumption breaks commands;
- contested Case claims yield one deterministic winner;
- adding/removing one app instance requires no domain/data rewrite.

Exact thresholds come from D-023/release performance plan.

---

## 7. Resilience/failure-drill suite

Staging must test at minimum:

1. worker restart with queued work;
2. duplicate job delivery;
3. notification provider outage;
4. provider timeout after possible acceptance;
5. duplicate webhook;
6. out-of-order webhook;
7. provider rate limiting;
8. queue backlog/burst;
9. transient DB failure during a command;
10. manual fallback from unavailable external fulfillment provider;
11. backup/restore procedure for release environment.

Pass conditions are defined by [RESILIENCE.md](RESILIENCE.md).

---

## 8. Pagination and bounded-query suite

All collections that can grow with tenants/users/events/cases must have bounded reads.

Tests must prove:

- server maximum page size;
- deterministic ordering;
- authorization/tenant filtering occurs correctly;
- pagination cannot expose another tenant's rows;
- normal UI does not require downloading complete case/audit/resource history;
- large fixture datasets do not create N+1 behavior on critical responder queues/resources.

---

## 9. MVP / production-readiness gates

The system is not production-ready until all required gates for the target release are `READY`.

| Gate | Pass condition |
|---|---|
| **AUTH** | Passwordless veteran auth; responder/admin MFA; session invalidation/rate limits |
| **CONSENT** | First-class grants; evaluation at use time; revocation stops future use |
| **CHECK-IN** | Versioned questionnaire; incomplete/abandoned handling; signal separate from Check-In |
| **COORDINATION** | Case/Service Request machines and responder actions conform |
| **EXTERNAL_FULFILLMENT** | Provider ports/adapters/manual fallback/idempotency/reconciliation suite green |
| **UI_CONFORMANCE** | MVP-reference visual/behavior + accessibility suite green |
| **SAFETY** | Red-state/human-review rules green; no emergency auto-dispatch/diagnosis |
| **PRIVACY** | Minimization/logging/non-prod-data rules; provider projection bounded |
| **SCALE** | Release target load profile, horizontal stateless app behavior, durable jobs, bounded queries pass |
| **RESILIENCE** | Failure drills, retries/dead-letter/reconciliation/backpressure/restore pass |
| **OPERATIONS** | Staffing, queue/resource review, provider health, incident/recovery paths exist |
| **REPORTING** | Allowed operational metrics can be produced; forbidden clinical claims absent |

Current value of all gates: `NOT_READY` unless [STATUS.md](STATUS.md) records otherwise.

---

## 10. Fixtures

- Use synthetic veterans only.
- Golden signal vectors wait on D-011; unreleased placeholders must be marked `UNRELEASED_FIXTURE`.
- Safety copy tests use `TEST_SAFETY_COPY` until D-012 closes.
- Provider adapters use fake provider fixtures until provider decisions close.
- Visual fixtures must not contain real veteran data.
- Load tests must not use production veteran data.

---

## 11. Non-goals

- Testing unnamed vendor SLAs as if contractual
- Using production veteran data in non-production tests
- Claiming clinical validation
- Requiring pixel-perfect prototype cloning
- Requiring a specific cloud/queue/cache/provider product
- Treating a small pilot dataset as sufficient scale evidence

---

## 12. Handoff

Implementation work may only claim conformance after relevant specs are released. Release/pilot readiness review must cite test evidence for every applicable gate.
