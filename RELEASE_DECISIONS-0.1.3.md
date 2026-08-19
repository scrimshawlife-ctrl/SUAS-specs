# RELEASE_DECISIONS-0.1.3.md — D-018 shelter adapter decision ledger

**Release:** `0.1.3`
**Owner:** `@scrimshawlife-ctrl`
**Owner action date:** `2026-08-19` PT
**Supersedes for D-018 only:** [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) D-018 boundary
**Base decision ledgers:** [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md) for D-017; [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) for all other D-001–D-025 entries
**Target:** implementation-authoritative specification release; **not** production-operating approval
**Production readiness:** `NOT_READY`

This ledger closes D-018 by naming Amadeus as the first commercial search/inventory adapter family for temporary shelter implementation planning. Amadeus remains an adapter-local realization behind the provider-neutral `TemporaryShelterPort`; `ManualShelterAdapter` remains mandatory. This decision does not authorize production deployment, live pilot operation, real veteran data, payment-card handling, or real room reservations.

| ID | Release status | v0.1.3 boundary |
|---|---|---|
| D-018 | `DECIDED` | Amadeus is selected as the first commercial search/inventory adapter family for `TEMPORARY_SHELTER_FULFILLMENT`. `ManualShelterAdapter` remains required. Search, availability, offer, property, rate, and reservation objects remain adapter-local. SUAS MUST NOT collect, transmit, proxy, tokenize, or store raw payment-card data. Reservation creation is `BLOCKED_BY_PAYMENT_ARCHITECTURE` unless a documented card-free enterprise contract permits the selected offer to be reserved without SUAS handling card data. No real Amadeus credential use, inventory access involving real veteran data, hold, reservation, cancellation, or provider disclosure is authorized until SPEC-018 readiness and production external-effect gates pass. |

## Consequences

1. Implementations may add an Amadeus shelter search/inventory adapter behind `TemporaryShelterPort` and the Provider Router.
2. Domain modules, canonical Service Request/Fulfillment states, API contracts, events, consent rules, and provider-neutral fulfillment semantics do not change.
3. `ManualShelterAdapter` and fake shelter adapters remain first-class. Search or reservation unavailability must fall back truthfully to another compatible adapter or manual coordination when policy permits.
4. Offer ranking must be deterministic, explainable, auditable, and based only on explicit operational inputs. It must not infer clinical need, hidden eligibility, or ability to pay.
5. Search and availability may be implemented with synthetic data and non-real sandboxes. Any mutation with an ambiguous result uses a stable FulfillmentAttempt idempotency identity and records `PROVIDER_UNKNOWN` until reconciled.
6. SUAS does not implement raw card capture, card vaulting, checkout, payment tokenization, or provider payment-form proxying. Reservation remains `BLOCKED_BY_PAYMENT_ARCHITECTURE` unless an owner-approved, documented card-free enterprise contract establishes a reservation path with no SUAS card handling.
7. D-010 funding/billing remains `FUTURE` / `DECISION_PENDING`. This decision does not define who pays, reimbursement, invoicing, benefit eligibility, guarantees, deposits, incidentals, or cancellation fees.
8. Provider-specific eligibility, occupancy, minor/guardian, accessibility, pet, identity-document, check-in, and cancellation rules are not canonical unless explicitly added by a later released contract. Unsupported requirements must fail to a human rather than be guessed.
9. Production use still requires SPEC-018 readiness evidence, validated credentials/secrets, provider health and fallback behavior, privacy/security review, field-level disclosure conformance, operational runbooks, and explicit environment authorization.
10. D-021 through D-024 remain `DECISION_PENDING`; this decision establishes no production capacity, SLO, alert, RTO, or RPO commitment.

## Unchanged release-wide safety boundary

v0.1.3 remains implementation-authoritative but not production-operating. It does **not** authorize:

- production deployment;
- operation with real veteran data;
- a live controlled pilot;
- real external service bookings or reservations;
- raw payment-card handling;
- production Support Signal thresholds;
- official safety/crisis copy;
- compliance claims;
- production capacity/SLO/RTO/RPO claims;
- small-cell aggregate reporting.

Those surfaces require SPEC-017 implementation conformance plus SPEC-018 readiness evidence and every applicable still-open decision closure.
