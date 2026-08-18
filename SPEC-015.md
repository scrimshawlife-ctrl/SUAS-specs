# SPEC-015.md — Owner acceptance record: release decisions and safe deferrals

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`  
**Decision ledger:** [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md)

The owner accepts the v0.1.0 release decision ledger and safe-deferral model.

## Target release classification

The first v0.1.0 released cut is an **implementation-authoritative specification release**, not a production-operating release.

Therefore a production decision may be `DEFERRED_FOR_RELEASE` only when the affected production capability is explicitly unavailable, manual-only, information-only, or future and no hidden implementation default is permitted.

## Accepted decision outcomes

- D-015 is `DECIDED`: full Case Notes are not veteran-visible in the v0.1 default.
- D-016 is `DECIDED`: v0.1 identity proofing remains self-attested veteran status + working passwordless contact channel; no VA/DD-214/in-person proofing requirement is added.
- D-010 remains future funding/billing.
- D-014 remains unavailable for this release; county/manual coverage is the implementation path.
- D-017–D-020 remain manual/fake/test-only; no production external service provider is selected.
- D-001–D-009, D-011–D-013, and D-021–D-025 are safely deferred because production deployment/live pilot/production signal/copy/SLO/recovery/sensitive aggregate reporting are explicitly unavailable in v0.1.0.

Every D-001–D-025 row is recorded as `DECIDED` or `DEFERRED_FOR_RELEASE` in the release ledger. No row uses an unspecified implementation default.

## Safety boundary

SPEC-015 acceptance does not authorize production deployment, real veteran data, a live pilot, official crisis copy, production Support Signal thresholds, compliance claims, real external bookings, production performance/recovery claims, or sensitive aggregate reporting.

Those remain gated by later decision closure plus SPEC-017 implementation conformance and SPEC-018 readiness evidence.
