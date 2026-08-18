# SPEC-005.md — Owner acceptance record: resources, referrals, fulfillment, follow-up, settlement

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the completion half of the SUAS coordination loop.

Accepted invariants:

- Resource freshness is distinct from live provider availability.
- Service Providers may be API-backed, manual, referral-only, or information-only.
- Referral send is not Fulfillment or proof of service receipt.
- Fulfillment Attempt is execution evidence and remains separate from canonical Service Request/Fulfillment state.
- Same-attempt retries reuse idempotency identity; deliberate reroute/provider switch creates a new attempt.
- `PROVIDER_UNKNOWN` reconciles before duplicate-risk mutation.
- Provider completion is evidence, not automatic confirmation.
- Follow-Up is first-class; notification/job retries are distinct from Follow-Up business retries.
- Blocking versus carried-forward Follow-Up is explicit.
- Settlement is a durable resolution-cycle record; reopen preserves prior Settlement and later resolution creates a new one.
- Resolve/Settlement creation is atomic, idempotent, and stale-state protected.
- Funding/billing remains `FUTURE`.

The SPEC-006 logical representations for Settlement history, Follow-Up schedule identity, current projections, and fulfillment/idempotency history are accepted as the implementation target.
