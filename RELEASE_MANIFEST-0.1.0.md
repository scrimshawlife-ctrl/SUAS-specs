# RELEASE_MANIFEST-0.1.0.md — First implementation-authoritative SUAS specification cut

**Release version:** `0.1.0`  
**Release date:** `2026-08-18` PT  
**Owner:** `@scrimshawlife-ctrl`  
**Substantive spec source commit:** `96631dc1cf09768a8fe5550e620e3e1bde5377c9`  
**Decision ledger:** [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md)  
**Lifecycle:** `released`  
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`  
**Production readiness:** `NOT_READY`

This manifest is the lifecycle authority for the named v0.1.0 specification cut. Where an inline file header still describes an earlier authoring state such as `draft`, this manifest and [STATUS.md](STATUS.md) control lifecycle for the artifacts listed below. The substantive technical content remains the reviewed content rooted at the source commit above plus owner acceptance/release metadata in this acceptance chain.

## Released artifact set

The following artifacts are released for implementation at stack version `0.1.0`:

- `README.md`
- `PRODUCT.md`
- `GLOSSARY.md`
- `AGENTS.md`
- `VERSIONING.md`
- `STATUS.md`
- `ROADMAP.md`
- `DECISIONS.md`
- `AUTH.md`
- `CONSENT.md`
- `PRIVACY.md`
- `SAFETY.md`
- `TRUSTED_CIRCLE.md`
- `SECURITY.md`
- `CHECKINS.md`
- `SUPPORT_SIGNALS.md`
- `CASES.md`
- `DISPATCH.md`
- `RESPONDER_WORKFLOWS.md`
- `RESOURCES.md`
- `REFERRALS.md`
- `FULFILLMENT.md`
- `FOLLOWUP.md`
- `SETTLEMENT.md`
- `DOMAIN_MODEL.md`
- `DATA_MODEL.md`
- `EVENT_MODEL.md`
- `ARCHITECTURE.md`
- `API.md`
- `APIS.md`
- `ADMIN.md`
- `NOTIFICATIONS.md`
- `MVP_REFERENCE.md`
- `PROVIDER_INTEGRATIONS.md`
- `SCALING.md`
- `RESILIENCE.md`
- `TESTING.md`
- `DEPLOYMENT.md`
- `OPERATIONS.md`
- `INCIDENT_RESPONSE.md`
- `ONBOARDING.md`
- `PILOT.md`
- `ANALYTICS.md`
- `COMPLIANCE.md` as a regime register only, not a compliance claim
- `SPEC-001.md` through `SPEC-016.md` as governance/release records
- `SPEC_AUDIT.md`

`FRICTION.md` remains analysis/proposal material and is **not** implementation authority unless a released artifact explicitly adopts one of its proposals.

## Runtime artifact pins

| Artifact | v0.1.0 release state |
|---|---|
| API contract | `/api/v0`, contract `0.1.0` |
| Event schema | `0.1.0` |
| QuestionnaireVersion | `UNAVAILABLE` for production; implementation scaffolding/test fixtures allowed |
| Support Signal `signal_version` | `UNAVAILABLE` for production until D-011 closes |
| Consent template | `UNAVAILABLE` for production until authored/reviewed |
| Notification templates | `UNAVAILABLE` for production; test templates allowed |
| Approved safety copy | `UNAVAILABLE` until D-012 closes |
| External provider adapters | no production vendor pins; manual/fake/test paths only as declared below |
| Production performance plan | `UNAVAILABLE` until D-021/D-023 close |
| Production recovery plan | `UNAVAILABLE` until D-024 closes |
| Aggregate reporting privacy policy | `UNAVAILABLE` until D-025 closes |

## Feature-availability manifest

| Surface | v0.1.0 release state |
|---|---|
| Veteran/product UI architecture | `ENABLED` for implementation, not production operation |
| Responder/QRF UI architecture | `ENABLED` for implementation, not production operation |
| Admin UI architecture | `ENABLED` for implementation, not production operation |
| Email | `UNAVAILABLE` in production; fake/sink test adapters allowed |
| SMS | `UNAVAILABLE` in production; fake/sink test adapters allowed |
| Geocoding/maps | `UNAVAILABLE` |
| Transportation external adapter | `MANUAL_ONLY` / fake test adapter |
| Shelter/room external adapter | `MANUAL_ONLY` / fake test adapter |
| Food external adapter | `MANUAL_ONLY` / fake test adapter |
| Peer-support external adapter | `MANUAL_ONLY`; internal/manual QRF path |
| Support Signal production scoring | `UNAVAILABLE` |
| Official red-state/safety copy | `UNAVAILABLE` |
| Small/sensitive aggregate reports | `UNAVAILABLE` |
| Future categories (`BENEFITS`, `HOUSING`, `HEALTHCARE_NAVIGATION`, `COMMUNITY`, `OTHER`) | `FUTURE` |
| Production deployment | `UNAVAILABLE` |
| Controlled pilot with real veterans | `UNAVAILABLE` pending SPEC-017/018 evidence and required decision closure |

## Release meaning

This cut authorizes the `scrimshawlife-ctrl/SUAS` implementation repository to build against the released contracts. It does **not** make any readiness gate `READY` and does not authorize handling real veteran data or operating a live pilot.

Next stage: **SPEC-017 implementation conformance**. Production/pilot operation remains blocked until **SPEC-018** and all applicable decisions/evidence are complete.
