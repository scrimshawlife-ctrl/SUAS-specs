# INCIDENT_RESPONSE.md — Technical and operational/safety incidents (SUAS v0.1)

**Status:** `draft` / `0.1.0`; SPEC-013 dependency-blocked.  
**Related:** [OPERATIONS.md](OPERATIONS.md), [RESILIENCE.md](RESILIENCE.md), [SCALING.md](SCALING.md), [SAFETY.md](SAFETY.md), [SECURITY.md](SECURITY.md), [EVENT_MODEL.md](EVENT_MODEL.md), [PILOT.md](PILOT.md)

---

## 1. Purpose

Define one evidence-preserving incident process with two coordinated tracks:

- **technical/security/reliability:** availability, integrity, confidentiality, tenant isolation, session/auth, DB/jobs/events/provider integrations;
- **operational/safety:** veteran-support failures, missed/late coordination, consent/privacy errors, red-state without actual coverage, false provider/QRF success claims.

Do not invent statutory deadlines, severity timers, or compliance notification obligations. D-006 remains open; any legal deadlines require accepted counsel-backed updates.

---

## 2. Process

```text
detect
  -> classify scope/track
  -> contain
  -> preserve evidence
  -> protect ongoing support work
  -> notify authorized owners
  -> remediate/reconcile/restore
  -> verify correctness
  -> review root cause + spec gap
  -> close with auditable record
```

Containment must avoid making the incident worse. Examples: disable one provider adapter rather than deleting Requests; stop bad notification sends without erasing Notification history; revoke compromised sessions without destroying Audit Events.

---

## 3. Incident classes

At minimum operations must recognize:

### Security / privacy
- cross-tenant read/write;
- compromised/revocation-not-enforced session;
- provider/webhook credential compromise or forged callback;
- unauthorized/provider over-disclosure;
- sensitive data in logs/non-prod;
- consent-bypass notification/referral/provider disclosure.

### Domain integrity / idempotency
- duplicate Case/assignment/Settlement cycle from concurrency/retry;
- duplicate external ride/room/food/peer action;
- command idempotency conflict/corruption;
- provider callback incorrectly mutating canonical state;
- stale Follow-Up job changing newer schedule state;
- event/domain divergence or missing required event after committed transition.

### Availability / capacity
- app/DB outage;
- durable queue backlog/stall/lost-visibility concern;
- dead-letter growth;
- worker crash loop;
- provider/channel outage/rate limiting;
- tenant noisy-neighbor starvation;
- restore/migration failure.

### Operational / veteran support
- `PROVIDER_UNKNOWN` not reconciled while need remains actionable;
- false successful booking/dispatch shown to user;
- QRF request with no actual coverage presented as responder-dispatched;
- missed overdue Follow-Up;
- resource/provider stale data causing failed coordination;
- red-state human-review coverage failure;
- incorrect Trusted Contact notification.

---

## 4. Containment examples

| Incident | Safe containment direction |
|---|---|
| Compromised session | revoke authoritative session/user/membership; verify cross-instance enforcement |
| Bad provider adapter | disable/circuit adapter; preserve open Requests/Attempts; route/manual fallback only by policy |
| Forged webhook | reject ingress; rotate credential as needed; reconcile affected Attempts from authoritative provider/SUAS records |
| Notification consent bug | stop future sends; preserve Notification/Audit history; re-evaluate affected logical sends |
| Queue backlog | backpressure/non-critical shedding; prioritize urgent support work; do not delete queued work |
| Duplicate external mutation | freeze further risky retries; reconcile external refs/idempotency; human review |
| Event publisher failure | preserve domain state/outbox; replay publication idempotently |
| Cross-tenant bug | disable affected path, preserve evidence, restrict access; do not bulk-delete affected records |
| Restore concern | isolate restored environment/workers until idempotency/pending provider/job state is reconciled |

No containment step auto-dispatches emergency services.

---

## 5. Evidence preservation

Preserve, as applicable:
- Domain/Audit Events;
- command idempotency records;
- Case/Request/Settlement/FulfillmentAttempt/Follow-Up state/history;
- queue/dead-letter job identity and safe error metadata;
- provider normalized callback/attempt references;
- session/auth challenge/revocation metadata;
- deployment/config version and feature/provider enablement state;
- relevant logs/metrics with sensitive content minimized.

Do not mutate/delete historical events or “clean up” the incident out of the Case/Notification/provider-attempt record.

Evidence exports are authorized/audited.

---

## 6. Protect ongoing support work

Technical containment must explicitly assess active support impact:

- which Cases/Requests/Follow-Ups are waiting on the failed capability;
- whether manual/alternate provider path is available;
- whether responders need a visible degraded-state queue/filter;
- whether notification/contact paths remain authorized/available;
- whether queued/unknown work needs human reconciliation.

An incident dashboard/backlog is operational; it is not a clinical risk score.

---

## 7. Roles / ownership

- **Detector:** user, responder, admin, monitoring, job, provider integration.
- **Technical incident owner:** SUAS Admin/platform/security operator according to operating assignment.
- **Operational/safety owner:** assigned Responder/Org Admin/SUAS Admin according to scope.
- **Domain owner:** responsible module/operator consulted for replay/reconciliation decisions.

Exact on-call staffing/coverage remains D-009/D-023 operating policy; do not claim 24/7 unless decided and staffed.

---

## 8. Remediation / recovery verification

Before closure, verify the relevant invariants, for example:
- tenant isolation restored;
- revoked sessions cannot act across instances;
- no duplicate Settlement/provider effect created by retry;
- pending/unknown provider Attempts reconciled;
- stale Follow-Up jobs suppressed;
- queue/dead-letter work accounted for;
- required events published/replayed once logically;
- restore does not replay external side effects unsafely;
- affected users/operators see truthful current state.

Patch deployment alone is not incident closure.

---

## 9. Review / specification feedback

Each significant incident review records:
- timeline and affected scope;
- detected symptoms;
- root/ contributing causes when known;
- containment/remediation;
- data/support impact;
- evidence references;
- tests/runbooks missing or failed;
- whether an accepted specification was wrong/incomplete or implementation violated it;
- follow-up owner.

Spec gaps return to `SUAS-specs`; implementation behavior does not silently become canon.

---

## 10. Testability

Tabletops/failure drills include at least:
1. cross-tenant data exposure;
2. red-state/Trusted Contact consent near-miss;
3. compromised session not initially revoked on another instance;
4. provider timeout causing possible duplicate booking;
5. forged/duplicate webhook;
6. queue backlog with urgent support work;
7. event publisher failure after domain commit;
8. restore with pending `PROVIDER_UNKNOWN` / queued jobs;
9. false QRF availability/dispatch presentation;
10. stale Follow-Up job after reschedule.

Each exercise identifies owner, evidence, containment, ongoing-support protection, and verification artifact.

---

## 11. Non-goals

- invented legal notification deadlines;
- invented incident severity response times;
- automated emergency dispatch;
- hiding backlog/provider failure;
- deleting evidence to simplify recovery;
- assuming deployment success equals incident resolution.
