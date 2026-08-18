# SPEC-007.md — Owner acceptance record: architecture, API, auth, notifications, admin

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the core application/API/auth/notification/admin contract.

Accepted invariants:

- Scalable modular monolith remains the default architecture; app correctness is stateless across instances and production-critical async work is durable by contract.
- `/api/v0` is the canonical v0 API version selector; growing lists are bounded/cursor-paginated.
- Unsafe commands use persistent idempotency; identical replays return the authoritative result and conflicting key reuse fails.
- Challenge consumption, Case resolution, assignment, and other contested commands use atomic one-winner semantics.
- Session/revocation/rate-limit correctness is shared across horizontally scaled instances; privileged roles require MFA.
- One Notification row represents one logical send; logical-send dedupe is distinct from transport retries and Follow-Up retries.
- Consent/basis is checked before external sends.
- Org Admin remains distinct from SUAS Admin.
- Provider adapter administration exposes normalized capability/configuration/health without secrets or vendor-domain state.
- Plane A product API and Plane B external capability boundaries are accepted.

Exact cloud, auth, email, SMS, DB, queue, and service-provider products remain deferred for the implementation-only v0.1.0 release.
