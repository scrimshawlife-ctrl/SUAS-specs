# RELEASE_MANIFEST-0.1.3.md — SUAS D-018 shelter adapter decision release

**Release version:** `0.1.3`
**Release date:** `2026-08-19` PT
**Owner:** `@scrimshawlife-ctrl`
**Substantive spec source commit:** `b83a0ed0249010a036e71acf926b1e75919ff16b`
**Supersedes:** `0.1.2`
**Base release:** [RELEASE_MANIFEST-0.1.2.md](RELEASE_MANIFEST-0.1.2.md)
**Decision ledgers:** [RELEASE_DECISIONS-0.1.3.md](RELEASE_DECISIONS-0.1.3.md) for D-018; [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md) for D-017; [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) otherwise unchanged
**Lifecycle:** `released`
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`
**Production readiness:** `NOT_READY`

## Patch scope

v0.1.3 is a decision-only provider-selection and adapter-safety patch for D-018. It names Amadeus as the first commercial search/inventory adapter family that may be implemented behind the provider-neutral `TemporaryShelterPort` while keeping `ManualShelterAdapter` mandatory.

This release does not change product roles, canonical domain state machines, public API semantics, event schema, safety/privacy principles, readiness gates, or D-001–D-016 and D-019–D-025 release boundaries.

It adds:

- [RELEASE_DECISIONS-0.1.3.md](RELEASE_DECISIONS-0.1.3.md) — D-018 decision ledger entry selecting Amadeus for adapter-local temporary-shelter search/inventory implementation.
- a released field-level shelter disclosure projection and deterministic D-018 conformance matrix;
- explicit payment architecture, health/fallback, idempotency, and ambiguous-outcome boundaries.

It also updates lifecycle, handoff, environment, provider, testing, deployment, and index documents while preserving v0.1.0 through v0.1.2 history.

## Released artifact set

All artifacts released by v0.1.2 remain released. v0.1.3 additionally releases the D-018 decision ledger, this manifest, and the patch-level cross-links and D-018 adapter constraints changed by this release.

Draft Rev 3 files remain draft unless explicitly marked otherwise. They do not redefine D-018 or this release.

## Runtime pins

- Expected specification stack identifier: `0.1.3`.
- API contract remains `/api/v0`; product/domain/API semantics are unchanged.
- Event schema remains `0.1.0` until explicitly revised.
- Production QuestionnaireVersion, Support Signal scoring, official safety copy, production deployment, live pilot operation, production performance/recovery plan, and sensitive aggregate reporting remain unavailable.
- Amadeus search/inventory implementation is adapter-local. Real credentials, veteran-data disclosure, inventory effects, holds, reservations, and cancellations remain unavailable until SPEC-018 readiness and environment gates pass.
- Reservation is `BLOCKED_BY_PAYMENT_ARCHITECTURE` unless a documented card-free enterprise contract permits the selected reservation path without SUAS collecting, transmitting, proxying, tokenizing, or storing raw card data.

## D-018 authority

Amadeus may be implemented only as an adapter-local realization of `TEMPORARY_SHELTER_FULFILLMENT` through `TemporaryShelterPort` and the Provider Router.

The following remain canonical:

1. `ManualShelterAdapter` is mandatory.
2. Provider SDKs, payloads, property/rate/offer identifiers, statuses, credentials, and reservation objects remain adapter-local.
3. Provider state never replaces canonical Service Request or Fulfillment state.
4. Every external mutation uses SUAS-side idempotency; ambiguous outcomes reconcile as `PROVIDER_UNKNOWN` before duplicate-risk retry.
5. Consent and the released field-level shelter projection are evaluated before provider transmission.
6. Search/ranking is deterministic, explainable, auditable, and based only on explicit operational inputs.
7. No raw payment-card handling is permitted. Unsupported payment-dependent reservation fails closed as `BLOCKED_BY_PAYMENT_ARCHITECTURE` and returns to a human/manual path.
8. Provider health, degradation, fallback, and manual continuity remain required.
9. Funding/billing, production workload, SLO, alert, RTO, and RPO decisions are not closed by D-018.

## Environment authority

[ENVIRONMENT.md](ENVIRONMENT.md) controls environment classes, configuration precedence, safe default modes, real-external-effect prohibition, startup validation, secret handling, and build provenance. Configuration may further disable a feature. It may not enable anything this release or an inherited decision ledger marks unavailable/future.

## Handoff authority

[HANDOFF.md](HANDOFF.md) remains the canonical start-here document. The active engineering stage remains SPEC-017.

## Readiness boundary

This patch makes no readiness gate `READY` and does not authorize production deployment, real veteran data, live pilot operation, real provider side effects, or payment-card handling.
