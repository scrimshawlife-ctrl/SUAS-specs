# CHANGELOG.md

Dates are America/Los_Angeles (PT). Lifecycle changes are owner-controlled.

---

## 0.1.0 — 2026-08-14 through 2026-08-18 — `draft`

**Bootstrap and production-readiness preflight of the SUAS v0.1 specification stack.**

### Product / authority bootstrap

- Established product identity, mission, roles, canonical loop, service categories, state machines, non-goals, consent, safety, privacy, architecture, API, operations, and test scaffolding.
- Established `SUAS-specs` as canonical and `SUAS` as implementation against released specs only.
- Lifecycle remains `draft`; implementation authority `NOT_YET_RELEASED`; pilot/production readiness `NOT_READY`.
- Added compliance register, API capability inventory, onboarding/bootstrap, owner-review workflow, friction analysis, contact-log commands, veteran-visibility/enrollment defaults, and Notification logical-send attempt history.

### Production-readiness hardening — 2026-08-18

Added:

- [MVP_REFERENCE.md](MVP_REFERENCE.md) — observed MVP visual/interaction authority and truthful production divergences;
- [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md) — provider-neutral transportation, temporary shelter/room, food, peer-support ports and Manual Adapter;
- [SCALING.md](SCALING.md) — stateless horizontal app, durable work, bounded APIs, PostgreSQL/tenant/backpressure/load doctrine;
- [RESILIENCE.md](RESILIENCE.md) — crash/retry/replay/stale-work/session/provider/event/restore failure semantics.

Expanded governance:

- Roadmap now SPEC-001 through SPEC-019.
- SPEC-001 remains `READY_FOR_REVIEW`.
- SPEC-002 through SPEC-015 have dependency-blocked preflight owner worksheets.
- SPEC-016 has a blocked first-release assembly checklist.
- First implementation-authoritative release remains SPEC-016; release is not launch readiness.
- Readiness model expanded to 12 gates: `AUTH`, `CONSENT`, `CHECK-IN`, `COORDINATION`, `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SAFETY`, `PRIVACY`, `SCALE`, `RESILIENCE`, `OPERATIONS`, `REPORTING`.

### Cross-artifact correctness repairs

- Provider booking/dispatch became explicit third-party disclosure with use-time consent/minimum-necessary projection.
- `FulfillmentAttempt` and provider-adapter configuration became first-class logical concepts.
- Support Signal compute gained stable replay identity/effective projection semantics.
- Event identity separated from command/job idempotency, correlation, causation, and request identity.
- Required Domain Event publication became replay-safe through transactional outbox/equivalent semantics.
- Case creation/claim/assignment/reassignment gained atomic one-winner semantics.
- Resource freshness separated from live provider availability.
- Referral send made replay-safe/idempotent.
- Follow-Up business retry count separated from Notification/job retry and gained schedule-version stale-job protection plus blocking/carry-forward semantics.
- Settlement became first-class multi-cycle history across Case reopen with deterministic current projection.
- Persistent command idempotency and canonical `/api/v0` version selection added.
- Auth challenge/session revocation/rate-control semantics made horizontally authoritative.
- Notification logical-send dedupe and durable callback-safe delivery semantics added.
- Provider-neutral admin configuration/health added without exposing secrets.
- Incident taxonomy expanded for queue/idempotency/event/provider/QRF truth/restore failures.
- TESTING reconciled to prove the new concurrency/idempotency/session/event/Settlement/provider/UI/scale/resilience invariants.

### MVP visual reference reconciliation

Observed reference interaction spine now explicitly covered: `TAKE ACTION`, `I NEED SUPPORT`, `I WANT TO SERVE`, QRF deploy/search/contact/cancel, Immediate Resources, category browsing, responder On Duty/QRF Dashboard, Quick Resource Share, Alerts/Chat/Home, and distinct admin surface.

Production divergences include:

- replace contradictory `No email` enrollment copy;
- no guaranteed “near you”/immediate QRF response without evidence;
- no continuous GPS requirement from prototype wording;
- crisis copy comes from accepted SAFETY/D-012;
- future category cards may remain informational but cannot create hidden released workflows;
- prototype statistics/clinical claims are not production facts by default.

### Epistemic corrections

- Removed unsupported numeric regional/multi-region capacity envelopes; D-021/D-023 own release-specific workload/performance evidence.
- Separated Request `CONFIRMED` fulfillment metrics from mere Fulfillment/provider `COMPLETED` state.
- Removed invented `<5` aggregate reporting suppression threshold.
- Opened D-025 for aggregate reporting privacy/small-cell aggregation/suppression policy.
- Clarified high traction does not silently expand the controlled pilot or count as outcome evidence.

### Decisions / release preparation

Open decisions now run D-001 through D-025.

SPEC-015 classifies release decisions as must-close, conditional, or safely deferrable only with an explicit unavailable/manual/default boundary permitted by accepted specs.

SPEC-016 requires a pinned release manifest, D-001–D-025 decision ledger, enabled/manual-only/information-only/unavailable/future feature manifest, runtime artifact pins, and cross-artifact consistency review before owner release.

### Current state

- Lifecycle: `draft`
- Implementation authority: `NOT_YET_RELEASED`
- Pilot readiness: `NOT_READY`
- Production readiness: `NOT_READY`
- All 12 readiness gates: `NOT_READY`
- Owner acceptance/release actions performed by agents: none
- Production application code/provider credentials/real veteran data added to this repository: none
