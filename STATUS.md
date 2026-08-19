# STATUS.md — SUAS specification status (v0.1.2)

**Specification lifecycle:** `released`
**Phase:** `IMPLEMENTATION_AUTHORIZED`
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`
**Release manifest:** [RELEASE_MANIFEST-0.1.2.md](RELEASE_MANIFEST-0.1.2.md)
**Decision ledger:** [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md) for D-017; [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) otherwise
**Pilot readiness:** `NOT_READY`
**Production readiness:** `NOT_READY`

## Governance frontier

SPEC-001 through SPEC-015 are accepted. SPEC-016 established the first released cut. v0.1.2 is a D-017 transportation adapter decision patch that supersedes v0.1.1 while preserving product/domain/API semantics. SPEC-017 implementation conformance is active. SPEC-018 remains the go/no-go stage for any real pilot or production operation.

## Current release additions

- [ENVIRONMENT.md](ENVIRONMENT.md) — canonical environment/config/startup-validation/secrets/provenance/migration contract.
- [HANDOFF.md](HANDOFF.md) — canonical implementation handoff and Fable start-here path.

## Release meaning

v0.1.2 authorizes implementation in `scrimshawlife-ctrl/SUAS`, including an Uber transportation adapter behind `TransportationPort`. It does not authorize production deployment, real veteran data, live pilot operation, production Support Signal thresholds, official safety copy, real external provider bookings, compliance claims, production SLO/RTO/RPO claims, or sensitive aggregate reporting.

## Readiness gates

All remain `NOT_READY`:

`AUTH`, `CONSENT`, `CHECK-IN`, `COORDINATION`, `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SAFETY`, `PRIVACY`, `SCALE`, `RESILIENCE`, `OPERATIONS`, `REPORTING`.

A gate changes only with reproducible evidence under [TESTING.md](TESTING.md).

## Decision boundary

D-017 is closed by [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md). D-001–D-016 and D-018–D-025 release handling remains as recorded in [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) unless later releases supersede them.

## Next stage

Proceed with SPEC-017 implementation conformance against release `0.1.2`, using [HANDOFF.md](HANDOFF.md) and [ENVIRONMENT.md](ENVIRONMENT.md) as mandatory implementation inputs.
