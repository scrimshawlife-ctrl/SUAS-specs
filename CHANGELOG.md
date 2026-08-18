# CHANGELOG.md

Dates are America/Los_Angeles (PT). Lifecycle changes are owner-controlled.

---

## 0.1.0 — 2026-08-18 — `released`

**First implementation-authoritative SUAS specification release.**

Owner `@scrimshawlife-ctrl` completed the staged acceptance chain and released v0.1.0 on 2026-08-18 PT.

### Governance

- SPEC-001 through SPEC-015: `accepted`.
- SPEC-016: `released`.
- Implementation authority: `RELEASED_FOR_IMPLEMENTATION`.
- Release manifest: [RELEASE_MANIFEST-0.1.0.md](RELEASE_MANIFEST-0.1.0.md).
- Release decision ledger: [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md).
- SPEC-017 is the current next stage: implementation conformance.
- SPEC-018 remains the launch-readiness gate.

### Released design baseline

The released cut includes the product/domain/API/auth/consent/safety/privacy/data/event/architecture/provider/UI/scaling/resilience/testing/operations/pilot/analytics contracts named in the release manifest.

Key released properties include:

- referenced MVP visual/interaction fidelity with truthful production divergences;
- provider-neutral transportation, temporary shelter/room, food, and peer-support capability ports with Manual Adapter paths;
- stateless horizontally scalable application semantics;
- durable async-work contract;
- persistent command idempotency distinct from event identity;
- replay-safe Domain Event publication;
- single-use/shared auth challenge/session revocation semantics;
- atomic one-winner Case/assignment/Settlement operations;
- deterministic current Support Signal/Settlement/assignment projections;
- multi-cycle Settlement history;
- Follow-Up schedule identity and stale-job protection;
- Notification logical-send dedupe;
- bounded/paginated API/query rules;
- vendor-neutral scaling/resilience/operations evidence contracts;
- controlled pilot scope separated from traction and technical capacity;
- operational analytics separated from clinical/causal claims.

### v0.1.0 safe deferrals

This is an implementation-authoritative release, **not** a production-operating release.

The following remain unavailable for production until their decisions/evidence close:

- production hosting/auth/email/SMS/database/job infrastructure;
- production legal/retention/partner/staffing/counsel posture;
- production Support Signal rules;
- official safety/crisis copy;
- real external transportation/shelter/food/peer adapters;
- production workload/SLO/RTO/RPO targets;
- small/sensitive aggregate-reporting policy.

D-015 and D-016 defaults were accepted; all other release-relevant decisions are recorded as `DECIDED` or `DEFERRED_FOR_RELEASE` in the release ledger.

### Readiness

- Pilot readiness: `NOT_READY`.
- Production readiness: `NOT_READY`.
- All 12 readiness gates remain `NOT_READY` pending implementation and evidence.

Release authorizes implementation only. It does not authorize real veteran data, a live pilot, or production deployment.

---

## 0.1.0 — 2026-08-14 through 2026-08-18 — pre-release history

The v0.1.0 bootstrap/preflight established the canonical product loop, provider-neutral architecture, MVP visual reference, scaling/resilience contracts, staged governance through SPEC-019, and the cross-artifact hardening that preceded this release. See [SPEC_AUDIT.md](SPEC_AUDIT.md) for the detailed audit record.
