# VERSIONING.md — Specification and artifact versioning (SUAS v0.1)

**Stack:** `0.1.4` / `released`
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`
**Release manifest:** [RELEASE_MANIFEST-0.1.4.md](RELEASE_MANIFEST-0.1.4.md)

## 1. Lifecycle

Each specification artifact lifecycle is `draft`, `accepted`, `released`, or `superseded`. Preflight is not a lifecycle state. For a named release, the release manifest is the lifecycle authority for listed artifacts.

A file's inline `draft` / `dependency-blocked` header is **superseded by the current release manifest's released-artifact set** (0.1.4): where an inline header and the manifest disagree, the manifest governs, and a stale inline `draft` marker on a manifest-released file is not authority. SPEC-0xx acceptance records state their stack version as of the current manifest.

## 2. Semantic versioning

- `0.1.0` — first implementation-authoritative release.
- `0.1.1` — patch-level handoff/environment clarification; no product/domain/API semantic change.
- `0.1.2` — D-017 transportation adapter decision patch selecting Uber for adapter-local implementation; no product/domain/API semantic change and no production-operating approval.
- `0.1.3` — D-018 shelter adapter decision patch selecting Amadeus for adapter-local commercial search/inventory, with mandatory manual fallback and payment-architecture guardrails; no product/domain/API semantic change and no production-operating approval.
- `0.1.4` — implementation-conformance codification patch adopting accepted Bucket I gaps (P-1..P-23); closes no D-0xx owner decision and changes no product roles, safety/privacy, API selector, or event schema. Documents two additive, backward-compatible logical-model fields already implemented and tested (`notifications` subject reference, `resources.contact_method_kind`) as owner-accepted codifications with this changelog notice.

Version rules:

- MAJOR: incompatible change to a released contract.
- MINOR: backward-compatible contract addition; in 0.x, an explicitly owner-approved breaking clarification may use MINOR with clear changelog notice.
- PATCH: non-semantic clarification, cross-link, operational/handoff detail, typo, or equivalent implementation guidance that does not change canonical behavior.

Git commit SHA is provenance, not the specification version.

## 3. Version identities must stay separate

Do not conflate:

1. **Specification stack version** — currently `0.1.4`.
2. **Application version** — owned by `scrimshawlife-ctrl/SUAS`; must declare which released spec it implements.
3. **API version** — `/api/v0`; unchanged by v0.1.4.
4. **Event schema version** — `0.1.0`; unchanged by v0.1.4.
5. **Database migration/schema version** — implementation-owned mechanism that must be explicit and compatibility-checked. Mechanism (0.1.4): the schema version is a monotonic integer equal to the highest applied numbered migration, recorded in a runner-owned bookkeeping table, and the build states the version it requires explicitly rather than inferring it from the application version ([ENVIRONMENT.md](ENVIRONMENT.md) §9).
6. **Runtime content versions** — QuestionnaireVersion, signal_version, consent/notification templates, approved safety copy when available.

These five parallel identities (plus runtime content versions) are distinct and independently versioned:

| Identity | Current value / mechanism |
|---|---|
| Specification stack | `0.1.4` |
| Application version | owned by `scrimshawlife-ctrl/SUAS`; declares the released spec it implements |
| API selector | `/api/v0` |
| Event schema | `0.1.0` |
| Database schema | monotonic integer = highest applied numbered migration (runner-owned table) |

## 4. Build provenance

Shared builds must expose application version/commit, released SUAS spec version, release manifest identifier, database/schema migration version where applicable, build timestamp/version, and environment class. See [ENVIRONMENT.md](ENVIRONMENT.md).

## 5. Release lineage

`0.1.1` supersedes `0.1.0` for implementation handoff. `0.1.2` supersedes `0.1.1` for D-017 adapter selection. `0.1.3` supersedes `0.1.2` for D-018 adapter selection. `0.1.4` supersedes `0.1.3` for implementation-conformance codification and inherits every decision ledger unchanged. [RELEASE_DECISIONS-0.1.3.md](RELEASE_DECISIONS-0.1.3.md) controls D-018; [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md) controls D-017; [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) remains inherited for other D-001–D-025 decisions.

## 6. Implementation citations

Implementation PRs cite released spec file/section, stack version, lifecycle, manifest, runtime pins, and applicable readiness/conformance contract. A semantic gap returns to specs rather than becoming an undocumented implementation default.

## 7. Release vs readiness

`released` means implementation-authoritative, not production-ready. SPEC-017 verifies implementation conformance; SPEC-018 verifies launch readiness. All readiness gates remain `NOT_READY` until evidence says otherwise.
