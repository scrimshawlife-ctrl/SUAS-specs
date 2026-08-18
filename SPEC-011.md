# SPEC-011.md — Owner acceptance record: resilience and degradation

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the resilience/degradation contract.

Accepted requirements:

- App/worker restart cannot lose acknowledged production-critical work by contract.
- Duplicate API/job/webhook delivery cannot duplicate logical business effects.
- Lost-response command replay reuses the authoritative result.
- Event/outbox recovery prevents permanent loss of required Domain Events after commit.
- Stale Follow-Up jobs cannot mutate newer schedules.
- User/membership/session revocation remains authoritative across instances and restarts.
- Provider unknown outcomes reconcile before duplicate-risk retry.
- Notification/provider outage preserves parent workflow correctness; manual fallback is valid where policy permits.
- Failed/dead-letter/quarantined work is inspectable and replay is audited.
- Restore preserves idempotency, Settlement history, and FulfillmentAttempt state needed to avoid duplicate real-world effects.
- Failure drills cover duplicate/out-of-order callbacks, worker restart, queue backlog, DB uncertainty, stale jobs, cross-instance revoke, event publisher crash, and restore with pending external work.

D-024 production RTO/RPO remains deferred for v0.1.0 because production operation is unavailable. No zero-downtime or multi-region guarantee is accepted.
