# RELEASE_MANIFEST-0.1.2.md — SUAS D-017 transportation adapter decision release

**Release version:** `0.1.2`
**Release date:** `2026-08-19` PT
**Owner:** `@scrimshawlife-ctrl`
**Supersedes:** `0.1.1`
**Base release:** [RELEASE_MANIFEST-0.1.1.md](RELEASE_MANIFEST-0.1.1.md)
**Decision ledger:** [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md) for D-017; [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) otherwise unchanged
**Lifecycle:** `released`
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`
**Production readiness:** `NOT_READY`

## Patch scope

v0.1.2 is a decision-only provider-selection patch for D-017 only. It closes the first production transportation adapter selection by naming Uber as the first API-backed transportation adapter family that may be implemented behind the provider-neutral `TransportationPort`.

This release does not change product roles, domain state machines, public API semantics, event schema, safety/privacy rules, readiness gates, or D-001–D-016 and D-018–D-025 release boundaries.

It adds:

- [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md) — D-017 decision ledger entry selecting Uber for API-backed transportation adapter implementation.

It also updates lifecycle/index/config/provider documents to point new implementers at the D-017 decision while preserving v0.1.0 and v0.1.1 release history.

## Released artifact set

All artifacts released by v0.1.1 remain released. v0.1.2 additionally releases the D-017 decision ledger and patch-level cross-links/version metadata changed by this release.

Draft Rev 3 files remain draft unless explicitly marked otherwise. [RIDES.md](RIDES.md) remains non-authoritative except where later released artifacts cite its guardrails as implementation constraints.

## Runtime pins

Runtime artifact availability remains identical to v0.1.1 except the expected specification stack identifier is now `0.1.2` and D-017 is no longer globally pending.

- API contract remains `/api/v0` with domain/API semantics unchanged.
- Event schema remains `0.1.0` until explicitly revised.
- Production QuestionnaireVersion, Support Signal scoring, official safety copy, production deployment, live pilot operation, production performance/recovery plan, and sensitive aggregate reporting remain unavailable.
- Real Uber booking, credential use, production webhook handling, and veteran-data disclosure remain unavailable until SPEC-018 readiness and environment external-effect gates pass.

## D-017 authority

Uber may be implemented only as an adapter-local realization of `TRANSPORTATION_FULFILLMENT` through `TransportationPort` and the Provider Router.

The following remain canonical:

1. Manual transportation coordination remains required.
2. Provider SDKs, payloads, statuses, quote fields, webhooks, and booking IDs remain adapter-local.
3. Provider status never replaces canonical Service Request or Fulfillment state.
4. Every external mutation requires SUAS-side idempotency.
5. Consent and minimum-necessary disclosure are evaluated before any provider transmission.
6. Funding/billing, maps/geocoding, workload, SLO, alert, RTO, and RPO decisions are not closed by D-017.

## Environment authority

[ENVIRONMENT.md](ENVIRONMENT.md) continues to control logical environment classes, configuration precedence, safe default modes, real-external-effect prohibition, startup validation, secret classes, build provenance, and migration compatibility rules.

Configuration may further disable a feature. It may not enable anything this release or the inherited decision ledger marks unavailable/future.

## Handoff authority

[HANDOFF.md](HANDOFF.md) remains the canonical start-here document for Fable or any new implementation owner. The active engineering stage remains SPEC-017.

## Readiness boundary

This patch does not make any readiness gate READY and does not authorize production deployment, real veteran data, live pilot operation, or real provider side effects.
