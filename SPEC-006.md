# SPEC-006.md — Owner acceptance record: domain, data, event, architecture reconciliation

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the reconciled logical model across DOMAIN_MODEL, DATA_MODEL, EVENT_MODEL, and ARCHITECTURE.

Accepted invariants:

- Every canonical entity has a clear logical write owner.
- Settlement is first-class and supports multiple immutable resolution cycles per Case.
- Command idempotency is persistent logical state distinct from Domain Event identity.
- Support Signal primary computation/effective projection is deterministic and history-preserving.
- One-active-case/exclusive assignment semantics produce deterministic winners under contention.
- Service Request state remains separate from provider/FulfillmentAttempt state and history.
- Follow-Up carries stale-job schedule identity and explicit `BLOCKING|CARRIED_FORWARD` resolution disposition.
- FulfillmentAttempt preserves stable external idempotency identity and `PROVIDER_UNKNOWN` reconciliation.
- Required Domain Events are replay-safe; outbox/equivalent publication is an allowed implementation mechanism.
- Tenant scoping and bounded/indexable production access are part of the logical contract.
- PostgreSQL physical constraints/indexes/ORM/hosting remain implementation choices only if they prove these invariants.

No cloud, queue, cache, provider, or physical migration technology is selected by this acceptance.
