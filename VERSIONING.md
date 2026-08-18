# VERSIONING.md — Specification and artifact versioning (SUAS v0.1)

**Stack:** `0.1.0` / `released`  
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`  
**Release manifest:** [RELEASE_MANIFEST-0.1.0.md](RELEASE_MANIFEST-0.1.0.md)

---

## 1. Lifecycle

Each specification artifact has exactly one lifecycle:

| State | Meaning | Implementation authority? |
|---|---|---|
| `draft` | under active edit/preflight | No |
| `accepted` | owner-reviewed/frozen for its roadmap stage | No |
| `released` | included in a named implementation-authoritative release cut | Yes |
| `superseded` | replaced by later released artifact | historical only |

Preflight is not a lifecycle state.

For a named release cut, the release manifest is the lifecycle authority for the artifacts it lists. If an older inline authoring header conflicts with the manifest, the manifest and [STATUS.md](STATUS.md) control implementation authority.

---

## 2. Semantic versioning

The first released stack is `0.1.0`.

- MAJOR: incompatible change to a released contract.
- MINOR: backward-compatible contract addition; while still in 0.x, an explicitly owner-approved breaking clarification may use a MINOR bump with changelog notice.
- PATCH: non-semantic clarification/cross-link/typo.

Git commit SHA is provenance, not the specification version.

---

## 3. Owner lifecycle action

Only `@scrimshawlife-ctrl` may accept or release.

The owner completed SPEC-001 through SPEC-015 acceptance and SPEC-016 release on `2026-08-18` PT.

- SPEC-001–SPEC-015: `accepted`.
- SPEC-016 / release `0.1.0`: `released`.
- Implementation authority now applies only to artifacts named in `RELEASE_MANIFEST-0.1.0.md`.
- Future revisions must preserve stage-specific owner review and release-manifest discipline.

---

## 4. Release manifest

A release is a pinned contract set, not “current main.”

The v0.1.0 manifest records:

- release version/date/owner;
- substantive source commit provenance;
- released artifact paths;
- runtime artifact pins or explicit unavailability;
- D-001–D-025 release decision ledger;
- enabled/manual-only/information-only/unavailable/future feature states;
- safe deferrals.

`FRICTION.md` remains non-authoritative analysis unless a released artifact explicitly adopts a proposal from it.

---

## 5. Runtime artifact pins

For v0.1.0:

- API contract: `/api/v0`, version `0.1.0`.
- Event schema: `0.1.0`.
- Production QuestionnaireVersion: unavailable.
- Production Support Signal `signal_version`: unavailable until D-011 closes.
- Production Consent/Notification templates and official safety copy: unavailable until their owning decisions/artifacts close.
- Production provider adapters, performance/recovery plans, and sensitive aggregate-reporting policy: unavailable as recorded in the release manifest.

Historical runtime records always retain the versions/identities used when created and are never silently rewritten.

---

## 6. Implementation citations

Implementation PRs cite:

1. released spec file/section;
2. released stack/artifact version;
3. lifecycle = `released` via the release manifest;
4. relevant runtime artifact pins;
5. applicable readiness/conformance contract;
6. release feature-availability boundary where relevant.

Implementation gaps return to specs. Code does not redefine canon.

---

## 7. Release vs readiness

`released` means implementation-authoritative. It does **not** mean production-ready.

- SPEC-017 verifies implementation conformance.
- SPEC-018 verifies launch readiness from test/load/failure/restore/operations evidence and required production decision closure.

All readiness gates remain `NOT_READY` until evidence says otherwise.
