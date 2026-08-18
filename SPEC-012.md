# SPEC-012.md — Owner review worksheet: testing and readiness gates

**Status:** `BLOCKED_BY_SPEC_002_THROUGH_SPEC_011`  
**Stack version:** `0.1.0` / `draft`  
**Owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-012  
**Required:** [TESTING.md](TESTING.md), [STATUS.md](STATUS.md)

Preflight only. No lifecycle advancement.

---

## 1. Objective

Accept a reproducible evidence contract for every production-readiness gate so readiness is based on test/runbook evidence rather than implementation existence or subjective confidence.

---

## 2. Owner checklist

- [ ] AUTH suite includes one-time challenge race, MFA, cross-instance revoke/rate-limit behavior.
- [ ] CONSENT/PRIVACY suite includes provider disclosure minimum-necessary projection and revocation.
- [ ] CHECK-IN suite includes replay-safe signal settlement/effective projection/event publication.
- [ ] COORDINATION suite includes one-winner Case creation/claim/assignment and stale-state protection.
- [ ] Settlement/Follow-Up tests cover idempotent resolution cycles, reopen history, blocking/carry-forward, stale schedule jobs.
- [ ] API suite covers persistent command idempotency and conflicting-key reuse.
- [ ] Notification suite covers logical-send dedupe distinct from delivery attempts/business coordination attempts.
- [ ] Provider conformance suite is vendor-neutral and requires Manual Adapter/fallback behavior.
- [ ] UI suite tests truthful QRF pending/no-availability behavior, future-category boundaries, verified resource data, and WCAG target.
- [ ] SCALE suite uses D-021/D-023 release workload evidence, not invented user-count claims.
- [ ] RESILIENCE suite exercises crash/replay/stale/session/event/restore as well as external provider failures.
- [ ] OPERATIONS/REPORTING gates require exercised/reproducible evidence.
- [ ] Production veteran data is excluded from non-production fixtures.

---

## 3. Gate names

Owner confirms the release gate set remains:

`AUTH`, `CONSENT`, `CHECK-IN`, `COORDINATION`, `EXTERNAL_FULFILLMENT`, `UI_CONFORMANCE`, `SAFETY`, `PRIVACY`, `SCALE`, `RESILIENCE`, `OPERATIONS`, `REPORTING`.

No gate becomes READY merely because its implementation exists.

---

## 4. Evidence rule

A gate status change must cite reproducible evidence: test run/build artifact/runbook exercise/load profile/failure drill/restore record as appropriate, against the released spec version and target environment.

Failures or waivers are explicit spec/governance records; they are not silently ignored.

---

## 5. Exit criteria

SPEC-012 may be owner-accepted after dependencies and when TESTING/STATUS agree on gate names and minimum evidence without unsupported targets or hidden implementation authority.

**Implementation handoff:** none until SPEC-016 release.
