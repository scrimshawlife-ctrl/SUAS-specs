# RELEASE_MANIFEST-0.1.6.md — SUAS Wave A editorial hygiene release

**Release version:** `0.1.6`
**Release date:** `2026-08-22` PT
**Owner:** `@scrimshawlife-ctrl`
**Supersedes:** `0.1.5`
**Base release:** [RELEASE_MANIFEST-0.1.5.md](RELEASE_MANIFEST-0.1.5.md)
**Decision ledgers:** inherited unchanged — [RELEASE_DECISIONS-0.1.5.md](RELEASE_DECISIONS-0.1.5.md) for D-012; [RELEASE_DECISIONS-0.1.3.md](RELEASE_DECISIONS-0.1.3.md) for D-018; [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md) for D-017; [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) otherwise. **v0.1.6 closes no D-0xx owner decision.**
**Lifecycle:** `released` (ratified by owner merge of this patch)
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`
**Production readiness:** `NOT_READY`

## Patch scope

v0.1.6 is an editorial hygiene patch. It makes already-released rules readable in the files implementers open first. It **invents no new product/domain behavior**, makes and closes **no owner decision**, and changes **no** product roles, safety/privacy principles, readiness gates, API selector (`/api/v0`), event schema (`0.1.0`), or canonical state machines.

Every change is one of:

1. **G-III-2 wording.** D-015 / D-016 were `DECIDED` for the v0.1 default in [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md). Domain files that still said `INFERRED` / "remains open" now use the decided defaults.
2. **G-III-3 projection pointer.** SPEC-003 historically pointed at "the projection defined in SPEC-006". The accepted selection rule is the 0.1.4 rule in [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md) §7.1 and [DATA_MODEL.md](DATA_MODEL.md) §4. This patch updates that pointer and records the already-implemented two-override / chain case.
3. **G-III-4 leftover headers.** High-traffic files keep their historical inline `draft` / `0.1.0` markers for provenance and add an **Authority** line: this manifest governs; the inline marker is stale ([VERSIONING.md](VERSIONING.md) §1).

D-011 production scoring remains `DECISION_PENDING` / `UNAVAILABLE`. D-012 / D-015 / D-016 decision *values* are unchanged.

## Two-override / chain rule (not a new selection algorithm)

0.1.4 already selected the effective Support Signal as: most recent by `computed_at`, ties broken by `support_signal_id` descending, an `OVERRIDE` superseding the signal it overrides. 0.1.4 left "two overrides of the same target" as an owner-confirm follow-up.

This patch transcribes the rule the implementation already runs (`effectiveSignal`): a row is excluded from the candidate set if any later row names it in `override_of_signal_id`; remaining candidates are ordered by `computed_at DESC`, then `support_signal_id DESC`. Two overrides of the same target both remain candidates (recency, then id, wins). A sequential override chain excludes each named target.

This is still selection, not scoring, and is independent of D-011.

## Not closed by this release

- **G-I-4** (`ServiceOffer` catalog vs `ProviderOffer` live join/supersession) remains open. No join exists in the released logical model or in `scrimshawlife-ctrl/SUAS` to transcribe. This patch does **not** invent one.
- D-011 scoring / thresholds / golden vectors / `signal_version` publication.
- G-I-28 *action* (the command that writes `priority_signal_level` / opens or updates a Support Case).
- Remaining Bucket I policy items (PARTIAL / DISPUTED / FAILED, Settlement shape, on-duty / chat / metrics, auth TTLs, templates).
- D-019, D-020, D-010, and the SPEC-018 / pilot cluster.
- Draft Rev 3 islands (D-026–D-032).

## Released artifact set

All artifacts released by v0.1.5 remain released. v0.1.6 additionally releases this manifest and the editorial clarifications above to `CASES.md`, `AUTH.md`, `PRODUCT.md`, `ONBOARDING.md`, `PILOT.md`, `PRIVACY.md`, `GLOSSARY.md`, `SUPPORT_SIGNALS.md`, `DATA_MODEL.md`, `SPEC-003.md`, plus lineage updates to `VERSIONING.md`, `CHANGELOG.md`, `README.md`, `HANDOFF.md`, `STATUS.md`, and `DECISIONS.md` (stack pointer only).

Per [VERSIONING.md](VERSIONING.md) §1, this manifest's released-artifact set overrides any stale inline `draft` header on a listed file.

Draft Rev 3 files remain draft unless explicitly marked otherwise. They are not redefined by this release.

## Runtime pins

- Expected specification stack identifier: `0.1.6`.
- API contract remains `/api/v0`; product/domain/API semantics are unchanged.
- Event schema remains `0.1.0`.
- Database schema version is unchanged by this release (editorial only; no migration).
- Production QuestionnaireVersion, Support Signal scoring/thresholds (D-011), production deployment, live pilot operation, real external provider effects, real public-safety notification, and sensitive aggregate reporting remain unavailable.

## Readiness boundary

This patch makes no readiness gate `READY` and does not authorize production deployment, real veteran data, live pilot operation, real provider side effects, automated emergency dispatch, or payment-card handling. SPEC-017 implementation conformance plus SPEC-018 readiness evidence remain required.
