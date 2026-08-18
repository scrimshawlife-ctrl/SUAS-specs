# SPEC-009.md — Owner acceptance record: provider-neutral service fulfillment

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the provider-neutral fulfillment contract.

Accepted requirements:

- `TransportationPort`, `TemporaryShelterPort`, `FoodSupportPort`, and `PeerSupportPort` are canonical capability boundaries.
- Integration modes may be API, webhook, deep link, phone, email, manual coordination, or none; manual paths are first-class.
- Provider Router operates above adapters and does not own canonical Service Request state.
- ProviderOffer/status/payload shapes are normalized into SUAS-owned contracts; provider-native states remain adapter-local.
- Every external mutation belongs to a FulfillmentAttempt with stable idempotency identity.
- Deliberate provider switch creates a new attempt; ambiguous outcomes become `PROVIDER_UNKNOWN` and reconcile before duplicate-risk retry.
- Webhooks are authenticated, deduplicated, out-of-order safe, and cannot authorize new disclosure or bypass canonical transitions.
- Provider disclosures are purpose-scoped/minimum-necessary.
- Provider outage preserves the Service Request and may degrade to another conforming/manual path.
- Provider replacement must not require domain-state changes.

D-017–D-020 remain deferred for v0.1.0; production external adapters are not selected. The release boundary is manual/fake/test-only as recorded in the release manifest.
