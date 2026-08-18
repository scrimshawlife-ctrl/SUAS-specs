# CHANGELOG.md

Format: version, date, lifecycle, summary. Dates are recorded in America/Los_Angeles (PT).

---

## 0.1.0 — 2026-08-14 to 2026-08-18 (PT) — `draft`

**Bootstrap and production-readiness hardening of the SUAS v0.1 specification stack.**

- Established product identity, mission, roles, canonical loop, and non-goals.
- Established Support Case and Service Request state machines.
- Established consent as first-class grants; safety non-goals; AI policy; Medi-Cal/billing boundary as `FUTURE`.
- Established cross-repo authority: `SUAS-specs` canonical; `SUAS` implementation.
- Phase remains `SPECIFICATION_BOOTSTRAP`. Implementation authority `NOT_YET_RELEASED`. Pilot and production readiness `NOT_READY`.
- Open decisions recorded in [DECISIONS.md](DECISIONS.md); none guessed.

### Bootstrap additions

- Added [COMPLIANCE.md](COMPLIANCE.md) — compliance register, not a compliance claim. D-013 opened for counsel review before pilot.
- Added [APIS.md](APIS.md) — necessary API/capability inventory.
- Added [ONBOARDING.md](ONBOARDING.md) — admin first-run bootstrap and first-time user experience.
- Added [SPEC-001.md](SPEC-001.md) — owner review worksheet. SPEC-001 remains `READY_FOR_REVIEW`; agents must not self-accept.
- Opened D-015 (Case Note veteran visibility) and D-016 (enrollment identity-proofing beyond self-attest). MVP defaults remain `INFERRED` operational rules only.
- Specified contact-log commands and notification-attempt shape.
- Added [FRICTION.md](FRICTION.md) — analysis/proposed paths only; no decision closure.

### Production-readiness hardening — 2026-08-18

- Added [MVP_REFERENCE.md](MVP_REFERENCE.md) — referenced MVP is the visual/interaction source of truth subject to production auth/safety/consent/privacy/accessibility/domain overrides.
- Added [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md) — provider-neutral transportation, temporary shelter/room, food-support, and peer-support capability ports; Manual Adapter is first-class.
- Added [SCALING.md](SCALING.md) — stateless horizontal application tier, durable work, concurrency, bounded APIs, PostgreSQL scaling doctrine, tenant fairness, load/observability requirements.
- Added [RESILIENCE.md](RESILIENCE.md) — timeout/retry/circuit/dead-letter/reconciliation/backpressure/restore/failure-drill requirements.
- Expanded readiness to 12 gates: `AUTH`, `CONSENT`, `CHECK-IN`, `COORDINATION`, `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SAFETY`, `PRIVACY`, `SCALE`, `RESILIENCE`, `OPERATIONS`, `REPORTING`.
- Expanded roadmap to SPEC-001 through SPEC-019. First implementation-authoritative released cut is SPEC-016.
- Added D-017 through D-024 for provider adapters, first-release capacity target, durable job/queue implementation, performance SLOs, and RTO/RPO. No provider or numeric target selected.
- Added `FulfillmentAttempt` domain/data semantics for idempotent external/manual fulfillment and unknown-outcome reconciliation.
- Reconciled architecture, API inventory, fulfillment, domain/data, testing, deployment, operations, pilot, authority, versioning, agent, and contributing rules to the scalable/provider-neutral model.
- Repaired stale governance references in `SPEC-001.md`, `SPEC_AUDIT.md`, `PILOT.md`, `VERSIONING.md`, `AGENTS.md`, and `CONTRIBUTING.md`.
- Clarified that SPEC-001 may accept high-level governing principles while detailed MVP/provider/scale/resilience contracts remain later independent roadmap stages.
- Added canonical glossary definitions for `Fulfillment Attempt` and `Provider Adapter`, and expanded `Capability Port` terminology to provider fulfillment ports.
- Updated [PRODUCT.md](PRODUCT.md) to state that Service Providers do not require APIs and that pilot size is not a production architecture ceiling.

### SPEC-002 preflight — 2026-08-18

- Extended [CONSENT.md](CONSENT.md) so provider fulfillment calls are explicit third-party disclosure surfaces requiring use-time consent/system-basis evaluation and minimum-necessary field projection.
- Extended [PRIVACY.md](PRIVACY.md) with provider payload minimization, no whole-case disclosure by default, and provider retention/deletion unknowns.
- Extended [SECURITY.md](SECURITY.md) with provider credential, webhook authentication/replay, idempotency, SSRF-style endpoint-control, response normalization, and duplicate external mutation controls.
- Added [SPEC-002.md](SPEC-002.md) as an owner-review worksheet with status `BLOCKED_BY_SPEC_001`. It does not bypass the SPEC-001 dependency and is not accepted/released.
- [SAFETY.md](SAFETY.md) and [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md) were preflighted and remained consistent; no lifecycle change was made.

No implementation code is included. No specification artifact is `accepted` or `released`.
