# RELEASE_DECISIONS-0.1.0.md — Owner release decision ledger

**Release:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`  
**Owner action date:** `2026-08-18` PT  
**Target:** implementation-authoritative specification release; **not** production-operating approval  
**Production readiness:** `NOT_READY`

This ledger satisfies SPEC-015 for the first implementation-authoritative cut by deciding or safely deferring every D-001–D-025 item. A deferral is permitted because the affected production capability remains explicitly unavailable, manual-only, information-only, or future in this release. No provider, legal conclusion, production threshold, or operational promise is inferred.

| ID | Release status | v0.1.0 boundary |
|---|---|---|
| D-001 | `DEFERRED_FOR_RELEASE` | Production hosting is `UNAVAILABLE`; implementation may use LOCAL/TEST/STAGING environments only until a later decision. |
| D-002 | `DEFERRED_FOR_RELEASE` | Production auth provider is `UNAVAILABLE`; implement provider-neutral AuthPort/contracts and fakes only. |
| D-003 | `DEFERRED_FOR_RELEASE` | Production SMS/phone OTP is `UNAVAILABLE`; test/sink adapters only. |
| D-004 | `DEFERRED_FOR_RELEASE` | Production email delivery is `UNAVAILABLE`; test/sink adapters only. |
| D-005 | `DEFERRED_FOR_RELEASE` | Production DB hosting is `UNAVAILABLE`; PostgreSQL remains the released logical engine. |
| D-006 | `DEFERRED_FOR_RELEASE` | No production/pilot operation or compliance claim is authorized; legal/HIPAA classification remains unresolved. |
| D-007 | `DEFERRED_FOR_RELEASE` | Production retention/deletion policy is not authorized; no production data operation is permitted. |
| D-008 | `DEFERRED_FOR_RELEASE` | No operating pilot partner is named; placeholders remain for implementation fixtures. |
| D-009 | `DEFERRED_FOR_RELEASE` | No production/pilot staffing or coverage claim is authorized. |
| D-010 | `DEFERRED_FOR_RELEASE` | Funding/billing remains `FUTURE`. |
| D-011 | `DEFERRED_FOR_RELEASE` | Production Support Signal scoring is `UNAVAILABLE`; only interface/test-fixture work may proceed. |
| D-012 | `DEFERRED_FOR_RELEASE` | Production safety/crisis copy is `UNAVAILABLE`; only test placeholder slots may be implemented. |
| D-013 | `DEFERRED_FOR_RELEASE` | Counsel review remains required before any controlled pilot operation. |
| D-014 | `DEFERRED_FOR_RELEASE` | Geocoding/maps is `UNAVAILABLE`; county/manual coverage is the released implementation path. |
| D-015 | `DECIDED` | v0.1 default accepted: Veterans do not see full Case Notes. |
| D-016 | `DECIDED` | v0.1 default accepted: self-attested veteran status + working passwordless contact channel; no VA/DD-214/in-person proofing requirement in the implementation contract. |
| D-017 | `DEFERRED_FOR_RELEASE` | External transportation adapter is `MANUAL_ONLY`/information/referral path until selected. |
| D-018 | `DEFERRED_FOR_RELEASE` | External shelter/room adapter is `MANUAL_ONLY`/information/referral path until selected. |
| D-019 | `DEFERRED_FOR_RELEASE` | External food adapter is `MANUAL_ONLY`/information/referral path until selected. |
| D-020 | `DEFERRED_FOR_RELEASE` | Peer support uses internal/manual QRF path; external adapter remains unavailable. |
| D-021 | `DEFERRED_FOR_RELEASE` | No production capacity envelope is authorized; production deployment remains unavailable. |
| D-022 | `DEFERRED_FOR_RELEASE` | No production durable-job product selected; released implementation must preserve the durable-job abstraction and may use test implementations only. |
| D-023 | `DEFERRED_FOR_RELEASE` | No production SLO/alert thresholds are authorized; production readiness remains blocked. |
| D-024 | `DEFERRED_FOR_RELEASE` | No production RTO/RPO is authorized; production readiness remains blocked. |
| D-025 | `DEFERRED_FOR_RELEASE` | Small/sensitive aggregate reporting is `UNAVAILABLE`; no suppression threshold may be invented. |

## Release-wide safety boundary

This release authorizes implementation of the specified product architecture and behaviors. It does **not** authorize:

- production deployment;
- operation with real veteran data;
- a live controlled pilot;
- real external service bookings;
- production Support Signal thresholds;
- official safety/crisis copy;
- compliance claims;
- production capacity/SLO/RTO/RPO claims;
- small-cell aggregate reporting.

Those surfaces require later decision closure plus SPEC-017 implementation conformance and SPEC-018 readiness evidence.
