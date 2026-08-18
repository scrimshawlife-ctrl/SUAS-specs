# RELEASE_MANIFEST-0.1.1.md — SUAS handoff-hardening patch release

**Release version:** `0.1.1`  
**Release date:** `2026-08-18` PT  
**Owner:** `@scrimshawlife-ctrl`  
**Supersedes:** `0.1.0`  
**Base release:** [RELEASE_MANIFEST-0.1.0.md](RELEASE_MANIFEST-0.1.0.md)  
**Decision ledger:** [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) unchanged  
**Lifecycle:** `released`  
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`  
**Production readiness:** `NOT_READY`

## Patch scope

v0.1.1 is a non-semantic handoff/operations clarification. It does not change product roles, domain state machines, API semantics, provider-neutral architecture, safety/privacy rules, readiness gates, or D-001–D-025 release boundaries.

It adds:

- [ENVIRONMENT.md](ENVIRONMENT.md) — canonical environment/configuration/startup-validation contract;
- [HANDOFF.md](HANDOFF.md) — single implementation handoff entrypoint and required engineering hygiene.

It also updates lifecycle/index documents to point new implementers at these contracts.

## Released artifact set

All artifacts released by v0.1.0 remain released with unchanged substantive semantics. v0.1.1 additionally releases `ENVIRONMENT.md` and `HANDOFF.md` and any patch-level cross-links/version metadata changed by this release.

## Runtime pins

Runtime artifact availability remains identical to v0.1.0 except the expected specification stack identifier is now `0.1.1`.

- API contract remains `/api/v0` with domain/API semantics unchanged.
- Event schema remains `0.1.0` until explicitly revised.
- Production QuestionnaireVersion, Support Signal scoring, official safety copy, production providers, production performance/recovery plan, and sensitive aggregate reporting remain unavailable as in v0.1.0.

## Environment authority

`ENVIRONMENT.md` now controls logical environment classes, configuration precedence, safe default modes, real-external-effect prohibition, startup validation, secret classes, build provenance, and migration compatibility rules.

Configuration may further disable a feature. It may not enable anything this release or the v0.1.0 decision ledger marks unavailable/future.

## Handoff authority

`HANDOFF.md` is the canonical start-here document for Fable or any new implementation owner. The active engineering stage remains SPEC-017.

## Readiness boundary

This patch does not make any readiness gate READY and does not authorize production deployment, real veteran data, live pilot operation, or real provider side effects.