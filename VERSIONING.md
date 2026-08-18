# VERSIONING.md — Specification and artifact versioning (SUAS v0.1)

**Stack:** `0.1.1` / `released`  
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`  
**Release manifest:** [RELEASE_MANIFEST-0.1.1.md](RELEASE_MANIFEST-0.1.1.md)

## 1. Lifecycle

Each specification artifact lifecycle is `draft`, `accepted`, `released`, or `superseded`. Preflight is not a lifecycle state. For a named release, the release manifest is the lifecycle authority for listed artifacts.

## 2. Semantic versioning

- `0.1.0` — first implementation-authoritative release.
- `0.1.1` — patch-level handoff/environment clarification; no product/domain/API semantic change.

Version rules:

- MAJOR: incompatible change to a released contract.
- MINOR: backward-compatible contract addition; in 0.x, an explicitly owner-approved breaking clarification may use MINOR with clear changelog notice.
- PATCH: non-semantic clarification, cross-link, operational/handoff detail, typo, or equivalent implementation guidance that does not change canonical behavior.

Git commit SHA is provenance, not the specification version.

## 3. Version identities must stay separate

Do not conflate:

1. **Specification stack version** — currently `0.1.1`.
2. **Application version** — owned by `scrimshawlife-ctrl/SUAS`; must declare which released spec it implements.
3. **API version** — `/api/v0`; unchanged by v0.1.1.
4. **Event schema version** — `0.1.0`; unchanged by v0.1.1.
5. **Database migration/schema version** — implementation-owned mechanism that must be explicit and compatibility-checked.
6. **Runtime content versions** — QuestionnaireVersion, signal_version, consent/notification templates, approved safety copy when available.

## 4. Build provenance

Shared builds must expose application version/commit, released SUAS spec version, release manifest identifier, database/schema migration version where applicable, build timestamp/version, and environment class. See [ENVIRONMENT.md](ENVIRONMENT.md).

## 5. Release lineage

`0.1.1` supersedes `0.1.0` for implementation handoff. The D-001–D-025 release decision ledger remains [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) because this patch changes no decision outcome.

## 6. Implementation citations

Implementation PRs cite released spec file/section, stack version, lifecycle, manifest, runtime pins, and applicable readiness/conformance contract. A semantic gap returns to specs rather than becoming an undocumented implementation default.

## 7. Release vs readiness

`released` means implementation-authoritative, not production-ready. SPEC-017 verifies implementation conformance; SPEC-018 verifies launch readiness. All readiness gates remain `NOT_READY` until evidence says otherwise.