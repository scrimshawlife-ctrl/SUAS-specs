# RESILIENCE.md — Production resilience and degradation contract (SUAS v0.1)

**Status:** `draft` / `0.1.0`; SPEC-011 dependency-blocked.  
**Related:** [ARCHITECTURE.md](ARCHITECTURE.md), [SCALING.md](SCALING.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [AUTH.md](AUTH.md), [FOLLOWUP.md](FOLLOWUP.md), [EVENT_MODEL.md](EVENT_MODEL.md), [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [TESTING.md](TESTING.md)

---

## 1. Purpose

SUAS must preserve workflow correctness, consent boundaries, auditability, durable history, and operator visibility when dependencies or internal workers are slow, unavailable, duplicated, delayed, stale, restarted, or ambiguous.

Resilience never means pretending an action succeeded.

---

## 2. Failure invariants

1. Dependency failure never silently closes/erases a Case or Request.
2. Retry/replay never creates duplicate logical Case, Settlement cycle, Notification, Referral send, or provider fulfillment effect.
3. Duplicate/out-of-order job/webhook/event delivery is safe.
4. Stale scheduled work cannot mutate newer Follow-Up/session/config state.
5. Notification/provider outage cannot bypass consent or corrupt parent workflow.
6. Ambiguous external mutation reconciles before duplicate-risk retry.
7. Session/user/membership revocation remains effective across app instances/restarts.
8. Required Domain Event publication can recover after crash without losing or duplicating the logical business fact.
9. Failed/unknown/pending/degraded states remain distinguishable and operator-visible.
10. Manual coordination remains available for service fulfillment when policy permits.
11. Recovery/replay is authorized and audited.
12. Failure handling never expands veteran-data disclosure.

---

## 3. Failure classes

Tests cover at least:
- app instance crash/restart;
- worker crash/restart;
- DB transient failure/connection loss after commit uncertainty;
- queue delay/backlog/duplicate delivery;
- event/outbox publisher crash between domain commit and external publication;
- same API command retried after lost response;
- concurrent Case claim/Settlement resolve;
- Follow-Up reschedule with old due job still queued;
- user/membership revoke while another app instance has cached session/auth data;
- notification provider timeout/outage/duplicate callback;
- fulfillment provider timeout/outage/rate limit/duplicate or out-of-order webhook;
- malformed/unauthenticated callback;
- stale resource/provider availability;
- abnormal tenant load.

---

## 4. Timeouts / retries

All external calls have finite timeouts. Values are configuration/release decisions, not canonical numbers in this spec.

Retries are bounded, observable, idempotent, backoff-based for transient failures, rate-limit aware, and disabled for known permanent failures until underlying state changes.

After exhaustion, durable work remains operator-visible/quarantined; it does not disappear.

A timeout/connection loss does not prove failure where a side effect/commit may have occurred.

---

## 5. Dead-letter / quarantine

Operators can inspect job/attempt identity, tenant, domain reference, failure category, attempt count, last attempt/error summary, replay-safety state, and whether manual intervention is required.

Replay is audited. Replay of the same logical action preserves its idempotency/computation/attempt identity; deliberate new action receives new identity.

---

## 6. Internal command replay

If client loses an HTTP response after a command may have committed:

- same `Idempotency-Key` + same canonical request returns/reuses the original authoritative outcome;
- conflicting payload reuse fails;
- retry cannot create another Case assignment, Referral send, Settlement cycle, or equivalent effect;
- idempotency state survives process restart/horizontal instances.

DB connection loss after commit uncertainty is handled through authoritative idempotency/domain lookup, not blind command recreation.

---

## 7. Event/outbox recovery

Required domain transition + Domain Event publication must provide exactly-once **observable logical business effect**, not a claim of exactly-once broker delivery.

If publisher crashes after domain commit:
- durable outbox/equivalent work remains discoverable;
- replay emits/reuses the one logical event identity/effect;
- consumers must tolerate duplicate transport delivery through idempotent processing;
- audit/event lag is observable.

---

## 8. Stale scheduled work

Follow-Up due/overdue work carries expected `schedule_version`/equivalent identity.

If Follow-Up is rescheduled/cancelled/completed before an old job executes, the old job detects mismatch and no-ops/audits. It must not restore old `DUE/OVERDUE` state or send stale coordination notifications.

The same principle applies to scheduled work whose authoritative version/state changed after enqueue.

---

## 9. Session / authorization resilience

Horizontal scaling/caching must not make revocation best-effort.

Tests must prove:
- revoke membership/user/session on one instance;
- requests routed to another healthy instance observe authoritative revoke within accepted security semantics;
- app restart/cache loss does not revive revoked credentials;
- challenge consume replay cannot succeed twice.

Exact security timing SLO belongs to D-023/release security policy.

---

## 10. Circuit breaking / provider unknown outcomes

Adapter operational states include healthy/degraded/rate-limited/unavailable/misconfigured.

A provider mutation with ambiguous response becomes `PROVIDER_UNKNOWN` and reconciles by idempotency/external reference/status where possible before duplicate-risk retry.

Router may choose another compatible adapter/manual path only through a deliberate new FulfillmentAttempt when policy permits.

For the D-017 Uber Guest Rides adapter, local/persistent idempotency is authoritative. A SUAS FulfillmentAttempt stores the logical attempt key, adapter operation, request fingerprint, provider request identifier when known, last observed provider status, reconciliation state, retry/backoff state, and enough audit metadata to distinguish duplicate retry from deliberate new attempt. Because provider-native create idempotency was not confirmed, the adapter must not assume an Uber create call can be safely replayed by provider idempotency key. Ambiguous create/cancel outcomes enter `PROVIDER_UNKNOWN`, then reconcile through stored external reference/status lookup where possible before any duplicate-risk mutation or before manual fallback creates a deliberate new attempt.

The adapter must handle Uber rate limits and transient failures with finite timeouts, bounded exponential backoff with jitter, circuit/rate-limited state, operator-visible DLQ or failed-work surface, and manual coordination fallback when policy permits. Backoff must not hide urgent support state, and manual fallback must record whether the original provider outcome remains unknown to avoid double dispatch.

---

## 11. Notification degradation

Unavailable email/SMS remains accurately degraded. Another channel may be used only if independently authorized by policy/consent. Duplicate generating jobs map to one logical Notification when dedupe identity matches. Provider callback cannot enqueue a new message by itself.

Notification transport retries do not become Follow-Up coordination attempts.

---

## 12. Database resilience

- No command reports success before commit.
- Transaction retry only when safe/idempotent.
- No external provider mutation proceeds without a durable SUAS attempt record.
- Migrations have production-appropriate forward-fix/rollback strategy.
- Restore must preserve Settlement/event/idempotency history needed to prevent duplicate side effects.

---

## 13. Backpressure / tenant fairness

Overload queues/slows/rejects lower-priority work instead of amplifying failure. Mechanisms may include per-adapter/tenant limits, bounded worker concurrency, endpoint limits, priority classes, and temporary retryable rejection for non-critical bulk/admin work.

Urgent support/human-review paths must not be silently starved by maintenance/reporting jobs.

---

## 14. Recovery objectives

D-024 owns release-specific RTO/RPO and remains `DECISION_PENDING`.

Architecture must support DB backup/restore, durable-job recovery/reconciliation, provider/config restoration through secret/config mechanisms, incident ownership, and post-restore duplicate-side-effect checks.

No RTO/RPO number is invented in this spec.

---

## 15. Graceful deploy/restart

Workers terminate without losing acknowledged work. Long-running work completes or returns to durable visibility. App deploy/restart does not invalidate valid sessions unless security policy intentionally does so and must not revive revoked sessions.

---

## 16. Resilience observability

Monitor dependency health, timeout/rate-limit/circuit state, retry/dead-letter age, ambiguous/reconciled provider outcomes, DB/queue/worker failures, event/outbox lag, command-idempotency conflicts, stale-job suppression, session/revoke anomalies, duplicate notification dedupe, Settlement resolve conflicts, and manual fallback use.

Alerts identify affected capability/tenant without sensitive veteran content.

---

## 17. Failure drills

Staging with synthetic data exercises at least:

1. notification provider unavailable;
2. fulfillment provider timeout after possible acceptance;
3. duplicate/out-of-order provider webhook, including invalid Uber HMAC when Uber webhook ingress is implemented;
4. worker restart with queued work;
5. queue backlog/burst;
6. DB transient failure/lost response around domain commit;
7. provider rate limiting/backoff/circuit behavior and manual fallback;
8. duplicate API command after lost response;
9. concurrent Settlement resolve using same/different idempotency keys;
10. Follow-Up reschedule followed by stale due job;
11. session/membership revoke followed by request on another app instance;
12. event/outbox publisher crash after domain commit before publication;
13. restore rehearsal with pending/unknown provider attempts and idempotency history.

Results/remediation are recorded.

---

## 18. RESILIENCE gate

`RESILIENCE = READY` only when:
- production-critical work survives restart;
- retries/replays are bounded/idempotent;
- failed work is inspectable/replay audited;
- duplicate jobs/webhooks/API retries are safe;
- stale scheduled work is suppressed;
- session revocation remains authoritative across instances;
- event publication recovers without lost logical business facts;
- ambiguous provider mutations reconcile;
- provider/notification outages preserve parent workflow correctness;
- backpressure/tenant fairness is verified;
- failure drills pass or have accepted mitigations;
- D-024 recovery objectives are set for the release and restore procedure is tested.

Current: `NOT_READY`.

---

## 19. Non-goals

Zero-downtime claim, infinite retries, hiding dependency failure, unsupported RTO/RPO promises, multi-region active-active requirement, or treating manual coordination as architecture failure.
