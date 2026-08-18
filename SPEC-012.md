# SPEC-012.md — Owner acceptance record: testing and readiness gates

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the reproducible evidence contract for SUAS readiness.

The canonical gate set is:

`AUTH`, `CONSENT`, `CHECK-IN`, `COORDINATION`, `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SAFETY`, `PRIVACY`, `SCALE`, `RESILIENCE`, `OPERATIONS`, `REPORTING`.

Accepted evidence requirements include:

- auth race/MFA/cross-instance revoke and rate-limit tests;
- provider disclosure minimization and consent revocation;
- replay-safe Check-In/Support Signal/event settlement;
- one-winner Case/assignment and stale-state tests;
- multi-cycle Settlement and stale Follow-Up schedule tests;
- persistent API idempotency and conflicting-key tests;
- Notification logical-send dedupe versus delivery/business retry separation;
- vendor-neutral provider adapter conformance with Manual Adapter/fallback;
- truthful QRF pending/no-availability, future-category, verified-resource, accessibility, and visual-regression tests;
- release-specific scale/load evidence rather than invented capacity claims;
- internal replay/stale/session/event/restore resilience drills as well as external dependency failures;
- exercised operations/reporting evidence.

No gate becomes `READY` because implementation exists. Gate changes require reproducible evidence against the released spec and target environment. All gates remain `NOT_READY` at this acceptance stage.
