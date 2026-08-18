# CHANGELOG.md

Dates are America/Los_Angeles (PT). Lifecycle changes are owner-controlled.

---

## Unreleased — draft Rev 3 fence-post contracts (not a version bump)

**Draft / not implementation authority.** Released `0.1.1` remains the implementation contract. These additions do not bump the stack and do not mark any artifact `accepted` or `released`.

- Added [FENCE_POSTS.md](FENCE_POSTS.md) — Rev 3 fence-post outcomes `G1`–`G14` as draft, testable contracts.
- Added [SURFACES.md](SURFACES.md) — anonymous public front door vs identified opt-in platform; crossing remains an affirmative act and declining costs nothing.
- Added [ISLANDS.md](ISLANDS.md) — island config schema, resolve-before-consume behavior, hardcoded 988 / Veterans Crisis Line fallback, and `island_id` isolation boundary.
- Added [RIDES.md](RIDES.md) — ride-adapter contract with human dispatch, minimized provider payloads, and cost guardrails that fail to a human.
- Opened D-026 through D-032 for island scope, dispatcher routing operations, resource-list curation, reporting/minimization, dual enrollment/minors, contracting entity, and volunteer-driver screening. D-017–D-025 remain the released production-adapter / scale / recovery ledger.
- Added an SB 903 / peer-support register row to [COMPLIANCE.md](COMPLIANCE.md) as a `NOT_COMPUTABLE` note only; no legal or compliance claim.

No implementation code is included.

---

## 0.1.1 — 2026-08-18 — `released`

**Handoff/environment hardening patch. No product/domain/API semantic change.**

- Added [ENVIRONMENT.md](ENVIRONMENT.md) with canonical `LOCAL|TEST|STAGING|PRODUCTION` classes, configuration precedence, startup fail-closed rules, secret classes, safe fake/manual adapter modes, build provenance, and schema/migration compatibility requirements.
- Added [HANDOFF.md](HANDOFF.md) as the canonical implementation/Fable start-here path, including read order, repository hygiene, slice definition-of-done, ambiguity protocol, environment/versioning expectations, and safety/provider hard stops.
- Added [RELEASE_MANIFEST-0.1.1.md](RELEASE_MANIFEST-0.1.1.md), superseding v0.1.0 for implementation handoff while preserving the v0.1.0 D-001–D-025 decision ledger.
- Updated README, STATUS, VERSIONING, DEPLOYMENT, and AGENTS to point to the handoff/environment contract and distinguish spec/app/API/event/schema/runtime versions.
- Production/pilot readiness and all 12 readiness gates remain `NOT_READY`.

---

## 0.1.0 — 2026-08-18 — `released`

**First implementation-authoritative SUAS specification release.**

Owner `@scrimshawlife-ctrl` completed SPEC-001 through SPEC-015 acceptance and released SPEC-016. The release established the consent-governed product/domain/API architecture, MVP visual authority, provider-neutral capability ports, scalable modular monolith, replay/idempotency/concurrency rules, testing/readiness gates, operations/resilience contracts, and safe production deferrals recorded in [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md).

Implementation authority became `RELEASED_FOR_IMPLEMENTATION`; pilot and production remained `NOT_READY`.

---

## 0.1.0 — 2026-08-14 through 2026-08-18 — pre-release history

The bootstrap/preflight established the canonical loop, staged governance, provider-neutral architecture, MVP reference, scale/resilience contracts, and cross-artifact reconciliation. See [SPEC_AUDIT.md](SPEC_AUDIT.md).
