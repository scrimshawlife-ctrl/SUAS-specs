# RESILIENCE.md — Production resilience and degradation contract (SUAS v0.1)

**Status:** `draft` / `0.1.0` / not implementation authority until accepted and released.  
**Related:** [ARCHITECTURE.md](ARCHITECTURE.md), [SCALING.md](SCALING.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [TESTING.md](TESTING.md)

---

## 1. Purpose

Production SUAS must remain operationally correct when dependencies are slow, unavailable, duplicated, delayed, or ambiguous. Resilience means preserving veteran-support workflow state, consent boundaries, auditability, and operator visibility under failure.

Resilience does not mean pretending a service succeeded when it did not.

---

## 2. Core invariants under failure

1. A dependency outage must not silently close or erase a Support Case or Service Request.
2. A retry must not create duplicate external fulfillment.
3. A duplicate webhook/event/job must be safe.
4. An out-of-order webhook must not reverse canonical state incorrectly.
5. A notification-provider outage must not bypass consent or corrupt case state.
6. A provider timeout with an unknown outcome must be reconciled before blind retry when duplicate fulfillment is possible.
7. Operator-visible status must distinguish `FAILED`, `DEGRADED`, `UNKNOWN`, and `PENDING` where those distinctions affect action.
8. Manual coordination must remain available for service fulfillment when automated integration is unavailable and policy permits.
9. Recovery/replay actions must be authorized and audited.
10. Failure handling must not expose more veteran data than normal operation.

---

## 3. Failure classes

Production design and tests must cover at least:

- application instance crash/restart;
- worker crash/restart;
- database transient connection failure;
- durable queue delay/backlog;
- duplicate job delivery;
- notification provider timeout/outage;
- provider API timeout/outage;
- provider rate limiting;
- malformed or unauthenticated webhook;
- duplicate webhook;
- delayed/out-of-order webhook;
- partial network response where external outcome is unknown;
- stale provider/resource availability;
- one tenant generating abnormal load.

---

## 4. Timeouts

Every external network call must have finite timeout behavior.

Timeout values are environment/configuration decisions and may differ by capability, but no request or worker may wait indefinitely for an external provider.

A timeout does not prove the provider failed. Where an external side effect may have occurred, the result becomes an ambiguous state requiring reconciliation.

---

## 5. Retry policy

Retries must be:

- bounded;
- observable;
- idempotent;
- backoff-based for transient failures;
- rate-limit aware when a provider returns retry guidance;
- disabled for errors that are clearly permanent unless an operator changes the underlying condition.

Automatic retries must not continue indefinitely.

After retry exhaustion, durable work enters an operator-visible failed/quarantined state rather than disappearing.

---

## 6. Dead-letter / quarantine handling

Production-critical async work must have an inspectable failed-work path.

Authorized operators must be able to determine:

- job/attempt identity;
- tenant/org;
- domain object reference;
- failure category;
- attempt count;
- last attempted time;
- last safe error summary;
- whether replay is safe;
- whether manual intervention is required.

Replay must create an Audit Event and preserve idempotency identity where replay represents the same logical attempt.

---

## 7. Circuit breaking

External provider adapters must prevent repeated calls to a dependency that is clearly failing or rate-limited.

Circuit state is operational adapter state, not domain state. At minimum adapters/router must distinguish:

- healthy;
- degraded;
- rate-limited;
- unavailable;
- misconfigured.

When a circuit is open, Provider Router may select another compatible adapter or manual coordination if policy permits.

---

## 8. Unknown external outcome

If SUAS sends an external mutation and loses the response, and duplicate fulfillment would be harmful, the Fulfillment Attempt must not be blindly recreated.

Required path:

```text
mutation sent
    |
response ambiguous
    v
PROVIDER_UNKNOWN
    |
reconcile by idempotency key / external lookup / provider status
    |----------------------|
    v                      v
known outcome         remains unknown
    |                      |
continue canonical    operator review / manual path
workflow
```

Examples include a ride request, room reservation, or other scarce/costly support allocation.

---

## 9. Database resilience

The application must distinguish retryable database failures from domain validation failures.

Rules:

- domain commands do not report success before their transaction commits;
- transaction retry is used only when safe and bounded;
- idempotency prevents a client retry after connection loss from duplicating consequential work;
- database outage does not cause external provider mutation to continue without a durable SUAS attempt record;
- migrations have rollback/forward-fix plans appropriate to production table size.

---

## 10. Notification degradation

If an email or SMS channel is unavailable:

- the channel is marked operationally unavailable/degraded;
- notifications remain recorded with accurate delivery state;
- retries obey the channel policy;
- another channel may be used only if consent and notification policy independently authorize it;
- `IN_APP` remains separate from external delivery status;
- no workflow state is marked fulfilled because a message was sent or attempted.

---

## 11. Provider degradation

If a transportation, temporary shelter, food, or peer-support provider is unavailable:

- the Service Request remains canonical and inspectable;
- active Fulfillment Attempts show their actual state;
- router may try another configured compatible provider with a new attempt identity;
- manual coordination may be selected;
- the user/responder must not be shown a false successful booking;
- failures are available to operations metrics without exposing unnecessary PII.

---

## 12. Backpressure

When demand exceeds immediate downstream capacity, SUAS must slow/admit/queue work rather than amplify failure.

Backpressure mechanisms may include:

- queueing;
- per-adapter concurrency limits;
- per-tenant limits;
- bounded worker concurrency;
- endpoint rate limits;
- temporary rejection with explicit retry semantics for non-critical bulk/admin work;
- priority classes for veteran-support work.

Safety-critical copy and human-support prioritization rules remain governed by [SAFETY.md](SAFETY.md); resilience mechanisms must not silently suppress required human review.

---

## 13. Recovery objectives

Exact RTO/RPO targets remain `DECISION_PENDING` until production infrastructure and business operating requirements are chosen.

The architecture must nevertheless support:

- backed-up PostgreSQL data;
- restoration testing;
- recovery of durable jobs or explicit reconciliation of their state;
- restoration of provider configuration/secrets through the deployment secret mechanism;
- documented incident ownership;
- verification that restored state does not cause duplicate external side effects.

---

## 14. Graceful deploy/restart behavior

Workers must handle termination without silently losing acknowledged work.

Application deploys/restarts must not invalidate valid sessions unless the release intentionally changes session/security policy.

Long-running jobs should either complete within a graceful window or return to durable queue visibility for safe retry.

---

## 15. Resilience observability

At minimum monitor:

- dependency health by adapter;
- error/timeout/rate-limit rate;
- circuit state;
- retry counts;
- dead-letter/quarantine count;
- oldest failed work;
- ambiguous provider outcomes;
- reconciliation success/failure;
- DB connectivity/error rate;
- queue age/depth;
- worker restart/failure rate;
- fallback to manual coordination.

Alerts should identify the affected capability and tenant scope when appropriate without embedding sensitive veteran content.

---

## 16. Game days / failure drills

Before production readiness, staging must exercise representative dependency failures with synthetic data.

Minimum drills:

1. notification provider unavailable;
2. one fulfillment provider times out after accepting an idempotent request;
3. duplicate webhook delivery;
4. worker restart with queued work;
5. queue backlog/burst;
6. database transient failure during a domain command;
7. provider rate limiting;
8. manual fallback from an unavailable external provider.

Results and remediation items must be recorded.

---

## 17. Acceptance gate: RESILIENCE

`RESILIENCE = READY` only when:

- production-critical work is durable across worker restart;
- retries are bounded/idempotent;
- dead-letter/quarantine work is inspectable and replay is audited;
- duplicate jobs/webhooks are safe;
- ambiguous external mutations reconcile before risky retry;
- provider outage preserves Service Request state and supports explicit fallback;
- notification outage preserves workflow correctness;
- backpressure behavior is verified under burst;
- staging failure drills in §16 pass or have accepted mitigations;
- recovery/restore procedure has been tested at least once for the release environment.

Current status: `NOT_READY`.

---

## 18. Non-goals

- Claiming zero downtime
- Hiding dependency failure from operators
- Infinite retries
- Multi-region active-active as an MVP requirement
- Treating manual coordination as a failure of the architecture
