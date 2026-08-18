# SPEC-016.md — First released specification cut record

**Status:** `released`  
**Released:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`  
**Release manifest:** [RELEASE_MANIFEST-0.1.0.md](RELEASE_MANIFEST-0.1.0.md)  
**Decision ledger:** [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md)

The owner confirms SPEC-001 through SPEC-015 are accepted and releases the named v0.1.0 specification cut for implementation.

## Release verification

- SPEC-001 through SPEC-015: `accepted`.
- D-001–D-025: each recorded as `DECIDED` or `DEFERRED_FOR_RELEASE` in the release ledger.
- No deferred item uses an unspecified implementation default.
- Canonical terminology/state machines/Settlement/signal/idempotency/provider/notification/MVP/scale/resilience semantics were cross-audited before release.
- API contract pin: `/api/v0`, contract `0.1.0`.
- Event schema pin: `0.1.0`.
- Production QuestionnaireVersion, Support Signal scoring, official safety copy, real external adapters, production SLO/RTO/RPO, and sensitive aggregate reporting remain unavailable as recorded in the manifest.

## Lifecycle effect

Artifacts named in `RELEASE_MANIFEST-0.1.0.md` are `released` and become implementation authority for stack version `0.1.0`.

`IMPLEMENTATION_AUTHORITY = RELEASED_FOR_IMPLEMENTATION`.

This release is **not** production readiness and does not authorize a live pilot or real veteran data. All readiness gates remain `NOT_READY` until SPEC-017 implementation conformance and SPEC-018 evidence/decision closure are complete.

## Next stage

SPEC-017 — implement and perform conformance review against the released v0.1.0 cut. Any implementation gap returns to specs; code does not redefine canon.
