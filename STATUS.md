# STATUS.md — SUAS specification status (v0.1.0)

**Specification lifecycle:** `released`  
**Phase:** `IMPLEMENTATION_AUTHORIZED`  
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`  
**Release manifest:** [RELEASE_MANIFEST-0.1.0.md](RELEASE_MANIFEST-0.1.0.md)  
**Release decision ledger:** [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md)  
**Pilot readiness:** `NOT_READY`  
**Production readiness:** `NOT_READY`

---

## 1. Governance frontier

| Stage | Status |
|---|---|
| SPEC-001 | `accepted` |
| SPEC-002 | `accepted` |
| SPEC-003 | `accepted` |
| SPEC-004 | `accepted` |
| SPEC-005 | `accepted` |
| SPEC-006 | `accepted` |
| SPEC-007 | `accepted` |
| SPEC-008 | `accepted` |
| SPEC-009 | `accepted` |
| SPEC-010 | `accepted` |
| SPEC-011 | `accepted` |
| SPEC-012 | `accepted` |
| SPEC-013 | `accepted` |
| SPEC-014 | `accepted` |
| SPEC-015 | `accepted` with v0.1.0 release decision ledger |
| SPEC-016 | `released` — first implementation-authoritative cut |
| SPEC-017 | `READY_TO_BEGIN` — implementation conformance |
| SPEC-018 | blocked by implementation/conformance + operational evidence |
| SPEC-019 | future post-launch revision |

The owner explicitly authorized and completed the acceptance/release chain on 2026-08-18 PT.

---

## 2. Release meaning

Artifacts named in [RELEASE_MANIFEST-0.1.0.md](RELEASE_MANIFEST-0.1.0.md) are implementation authority for stack version `0.1.0`.

This release authorizes implementation in `scrimshawlife-ctrl/SUAS`. It does **not** authorize:

- production deployment;
- operation with real veteran data;
- a live controlled pilot;
- production Support Signal thresholds;
- official safety/crisis copy;
- real external provider bookings;
- compliance claims;
- production capacity/SLO/RTO/RPO claims;
- small/sensitive aggregate reporting.

Those surfaces remain unavailable until their decisions close and SPEC-017/018 evidence passes.

---

## 3. Readiness gates

All remain `NOT_READY`:

`AUTH`, `CONSENT`, `CHECK-IN`, `COORDINATION`, `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SAFETY`, `PRIVACY`, `SCALE`, `RESILIENCE`, `OPERATIONS`, `REPORTING`.

A gate changes only with reproducible evidence under [TESTING.md](TESTING.md). Release or implementation existence is not readiness evidence.

---

## 4. Release decision state

D-001–D-025 are resolved for this implementation-only release as `DECIDED` or `DEFERRED_FOR_RELEASE` in [RELEASE_DECISIONS-0.1.0.md](RELEASE_DECISIONS-0.1.0.md).

Notable v0.1.0 decisions:

- D-015 accepted default: Veterans do not see full Case Notes.
- D-016 accepted default: self-attested veteran status + working passwordless contact channel; no VA/DD-214/in-person proofing requirement in the implementation contract.
- D-010 funding/billing remains `FUTURE`.
- Production infrastructure, legal posture, staffing, production signal rules, official safety copy, production service providers, workload/SLO/recovery targets, and sensitive aggregate-reporting policy remain deferred and therefore unavailable for production use.

---

## 5. Next stage

Proceed with **SPEC-017 implementation conformance** against release `0.1.0`.

Implementation must cite the released artifact/section/version and return any gap to `SUAS-specs`; code does not redefine canon.

SPEC-018 remains the go/no-go stage for any real pilot or production operation.
