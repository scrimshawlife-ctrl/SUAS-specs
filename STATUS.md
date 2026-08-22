# STATUS.md — SUAS specification status (v0.1.6)

**Specification lifecycle:** `released`
**Phase:** `IMPLEMENTATION_AUTHORIZED`
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`
**Release manifest:** [RELEASE_MANIFEST-0.1.6.md](RELEASE_MANIFEST-0.1.6.md)
**Decision ledger:** inherited unchanged — [RELEASE_DECISIONS-0.1.5.md](RELEASE_DECISIONS-0.1.5.md) for D-012; [RELEASE_DECISIONS-0.1.3.md](RELEASE_DECISIONS-0.1.3.md) for D-018; [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md) for D-017; [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md) otherwise. **v0.1.6 closes no D-0xx.**
**Pilot readiness:** `NOT_READY`
**Production readiness:** `NOT_READY`

## Governance frontier

SPEC-001 through SPEC-015 are accepted. SPEC-016 established the first released cut. v0.1.6 is a Wave A editorial hygiene patch that supersedes v0.1.5 while preserving product/domain/API semantics and every decision-ledger value. SPEC-017 implementation conformance is active. SPEC-018 remains the go/no-go stage for any real pilot or production operation.

## Current release additions

- Domain-file wording for D-015 / D-016 now matches the v0.1 defaults already `DECIDED` in [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md).
- SPEC-003 points at the 0.1.4 effective-signal selection rule in [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md) §7.1 / [DATA_MODEL.md](DATA_MODEL.md) §4, including the two-override / chain case.
- Leftover high-traffic inline `draft` headers are stamped stale; this manifest governs ([VERSIONING.md](VERSIONING.md) §1).

Inherited from v0.1.5: [SAFETY_COPY.md](SAFETY_COPY.md) and the D-012 copy/destination/truthfulness contract. Inherited from earlier patches: [ENVIRONMENT.md](ENVIRONMENT.md), [HANDOFF.md](HANDOFF.md), adapter-local D-017/D-018, and the 0.1.4 conformance codifications.

## Release meaning

v0.1.6 authorizes implementation in `scrimshawlife-ctrl/SUAS` against the same product/domain/API contract as v0.1.5, with editorial contradictions removed. It does not authorize production deployment, real veteran data, live pilot operation, payment-card handling, real external provider bookings/reservations, production Support Signal thresholds (D-011), compliance claims, production SLO/RTO/RPO claims, or sensitive aggregate reporting.

## Readiness gates

All remain `NOT_READY`:

`AUTH`, `CONSENT`, `CHECK-IN`, `COORDINATION`, `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SAFETY`, `PRIVACY`, `SCALE`, `RESILIENCE`, `OPERATIONS`, `REPORTING`.

A gate changes only with reproducible evidence under [TESTING.md](TESTING.md).

## Decision boundary

D-012 is closed by [RELEASE_DECISIONS-0.1.5.md](RELEASE_DECISIONS-0.1.5.md). D-017 is closed by [RELEASE_DECISIONS-0.1.2.md](RELEASE_DECISIONS-0.1.2.md). D-018 is closed by [RELEASE_DECISIONS-0.1.3.md](RELEASE_DECISIONS-0.1.3.md). D-015 and D-016 remain the v0.1 defaults decided in [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md). D-011 and D-019–D-025 remain open unless later releases supersede them.

## Next stage

Proceed with SPEC-017 implementation conformance against release `0.1.6`, using [HANDOFF.md](HANDOFF.md) and [ENVIRONMENT.md](ENVIRONMENT.md) as mandatory implementation inputs. Do not re-pin the implementation repository until this manifest is owner-merged.
