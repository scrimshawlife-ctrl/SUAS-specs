# RELEASE_DECISIONS-0.1.2.md — D-017 transportation adapter decision ledger

**Release:** `0.1.2`
**Owner:** `@scrimshawlife-ctrl`
**Owner action date:** `2026-08-19` PT
**Supersedes for D-017 only:** [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) D-017 boundary
**Base decision ledger:** [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) for all other D-001–D-025 entries
**Target:** implementation-authoritative specification release; **not** production-operating approval
**Production readiness:** `NOT_READY`

This ledger closes D-017 by naming Uber as the first API-backed transportation adapter family for implementation planning. It does not authorize production deployment, live pilot operation, real veteran data, or real external ride bookings. All provider-specific semantics remain adapter-local and subordinate to provider-neutral SUAS fulfillment contracts.

| ID | Release status | v0.1.2 boundary |
|---|---|---|
| D-017 | `DECIDED` | Uber is selected as the first API-backed transportation adapter family for `TRANSPORTATION_FULFILLMENT`. The manual transportation path remains required. Provider SDKs, payloads, webhook statuses, quote fields, and booking objects remain adapter-local. No real Uber booking, credential use, production webhook, or veteran-data disclosure is authorized until SPEC-018 readiness and production external-effect gates pass. |

## Consequences

1. Implementations may add an Uber transportation adapter behind `TransportationPort` and the Provider Router.
2. Domain modules, canonical state machines, API contracts, events, Case semantics, Fulfillment state, consent rules, and minimum-necessary projections do not change.
3. Manual and fake transportation adapters remain first-class and must remain available for LOCAL/TEST and failover/manual coordination where policy permits.
4. Any Uber sandbox or test harness must use synthetic data and must not create rides, contact real riders/drivers, charge payment instruments, or disclose real veteran/provider data.
5. Production use still requires SPEC-018 readiness evidence, validated credentials/secrets handling, webhook verification where applicable, operational runbooks, privacy/security review, and explicit environment authorization.
6. D-010 funding/billing remains `FUTURE` / `DECISION_PENDING`; this decision does not define who pays, reimbursement, invoicing, benefit eligibility, or payment-card handling.
7. D-014 geocoding/maps remains `DECISION_PENDING`; adapter implementation must not smuggle in a canonical maps/geocoding product decision.
8. D-021 through D-024 remain `DECISION_PENDING`; this decision does not establish capacity, SLO, alert, RTO, or RPO commitments.

## Unchanged release-wide safety boundary

v0.1.2 remains implementation-authoritative but not production-operating. It does **not** authorize:

- production deployment;
- operation with real veteran data;
- a live controlled pilot;
- real external service bookings;
- production Support Signal thresholds;
- official safety/crisis copy;
- compliance claims;
- production capacity/SLO/RTO/RPO claims;
- small-cell aggregate reporting.

Those surfaces require SPEC-017 implementation conformance plus SPEC-018 readiness evidence and any still-open decision closure.
