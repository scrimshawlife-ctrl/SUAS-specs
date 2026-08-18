# CHANGELOG.md

Format: version, date, lifecycle, summary. Dates are recorded in America/Los_Angeles (PT).

---

## 0.1.0 — 2026-08-18 (PT) — `draft`

**Production-hardening specification pass (still draft; no implementation authority).**

- Added [MVP_REFERENCE.md](MVP_REFERENCE.md): referenced MVP is now the explicit visual/interaction source of truth, with `MUST_MATCH`, `MUST_PRESERVE_BEHAVIOR`, `MAY_EVOLVE`, and `MUST_CHANGE_FOR_PRODUCTION` conformance classes.
- Added [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md): provider-neutral transportation, temporary shelter/room, food, and peer-support capability ports; Manual Adapter remains first-class; provider-specific SDK/status/payloads remain adapter-local.
- Added [SCALING.md](SCALING.md): capacity bands, stateless horizontal application invariant, durable async work, atomic contention rules, bounded/paginated APIs, PostgreSQL scaling doctrine, tenant fairness, load profiles, observability.
- Added [RESILIENCE.md](RESILIENCE.md): finite timeouts, bounded retries, circuit breaking, dead-letter/quarantine, ambiguous provider outcome reconciliation, backpressure, failure drills, backup/restore expectations.
- Updated [ARCHITECTURE.md](ARCHITECTURE.md) from pilot-shaped modular monolith to scalable modular monolith while retaining the no-premature-microservices rule.
- Updated [APIS.md](APIS.md) so external service fulfillment is capability-port based and provider-agnostic, including `DURABLE_JOB_EXECUTION` and the four MVP service-fulfillment capabilities.
- Updated [FULFILLMENT.md](FULFILLMENT.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), and [DATA_MODEL.md](DATA_MODEL.md) with `FulfillmentAttempt`, provider adapter configuration, idempotency, manual fallback, normalized provider status, and `PROVIDER_UNKNOWN` reconciliation semantics.
- Updated [TESTING.md](TESTING.md) with provider adapter conformance, visual regression/accessibility, load/performance, resilience/failure-drill, and bounded-query suites.
- Expanded readiness gates in [STATUS.md](STATUS.md): `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SCALE`, and `RESILIENCE` are now explicit and remain `NOT_READY`.
- Expanded [ROADMAP.md](ROADMAP.md) to SPEC-001 through SPEC-019 so MVP visual conformance, provider neutrality, scaling, resilience, and release-specific decision closure occur before the first released specification cut.
- Added D-017 through D-024 in [DECISIONS.md](DECISIONS.md) for actual provider adapters, target production capacity band, durable job/queue implementation, performance SLOs, and RTO/RPO. No vendor or numeric value was guessed.
- Updated [README.md](README.md) to index the new production contracts and state the governing rule: pilot scope may be small, but architectural ceilings should not be.

No provider is selected. No cloud/queue/cache product is selected. No specification artifact is accepted or released by this pass.

---

## 0.1.0 — 2026-08-14 (PT) — `draft`

**Bootstrap of the SUAS v0.1 specification stack.**

- Created the complete markdown specification set under this repository.
- Established product identity, mission, roles, canonical loop, and non-goals.
- Established Support Case and Service Request state machines.
- Established consent as first-class grants; safety non-goals; AI policy; Medi-Cal/billing boundary as `FUTURE`.
- Established cross-repo authority: `SUAS-specs` canonical; `SUAS` implementation.
- Phase set to `SPECIFICATION_BOOTSTRAP`. Implementation authority `NOT_YET_RELEASED`. Pilot readiness `NOT_READY`.
- Open decisions recorded in [DECISIONS.md](DECISIONS.md); none guessed.

**Same-day addition (still `0.1.0` / `draft`; not a bump to 0.2.0):**

- Added [COMPLIANCE.md](COMPLIANCE.md) — compliance register (not a claim of being compliant). D-013 opened for counsel review before pilot.
- Added [APIS.md](APIS.md) — necessary-API inventory (Plane A minimum endpoints; Plane B capability ports). D-014 opened for geocoding/maps.
- Added [ONBOARDING.md](ONBOARDING.md) — admin first-run bootstrap and first-time user experience.

**Same-day SPEC-001 pass:**

- Added [SPEC-001.md](SPEC-001.md) — owner review worksheet. SPEC-001 status `READY_FOR_REVIEW`. Agents must not self-accept.
- Opened D-015 and D-016. Both remain open; MVP defaults are `INFERRED` operational rules only.
- Specified responder contact-log commands and notification-attempt shape.
- Recorded MVP enrollment and veteran visibility defaults without inventing VA identity integration.

**Same-day friction analysis:**

- Added [FRICTION.md](FRICTION.md) — user and infrastructure friction; proposed engineering paths only.
- Proposed, but did not accept, narrow closes for D-014 and D-012.

No implementation code is included. No specification artifact is `accepted` or `released`.
