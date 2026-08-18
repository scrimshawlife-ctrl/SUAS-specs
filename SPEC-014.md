# SPEC-014.md — Owner review worksheet: controlled pilot and analytics

**Status:** `BLOCKED_BY_SPEC_001_SPEC_012_SPEC_013`  
**Stack version:** `0.1.0` / `draft`  
**Owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-014  
**Required:** [PILOT.md](PILOT.md), [ANALYTICS.md](ANALYTICS.md), [OPERATIONS.md](OPERATIONS.md), [DECISIONS.md](DECISIONS.md)

Preflight only. No lifecycle advancement.

---

## 1. Objective

Accept the controlled Santa Clara County pilot as an operating cohort and accept operational analytics definitions without confusing traction, throughput, or fulfillment workflow data with clinical outcomes.

---

## 2. Owner checklist

### Pilot boundary
- [ ] Controlled pilot remains approximately 25–50 enrolled veterans unless explicitly changed.
- [ ] Demand/traction above cohort capacity does not silently expand the pilot.
- [ ] Waitlist/interest, if later implemented, is distinct from PilotEnrollment.
- [ ] Pilot cohort size is not a technical capacity ceiling; D-021 owns release workload capacity.
- [ ] D-008 partner organizations and D-009 staffing/coverage are required before named/operational launch claims.
- [ ] D-013 counsel review is required before pilot operation and is not a compliance claim.

### Provider / operations
- [ ] Enabled provider adapters pass conformance or affected capability is manual/unavailable explicitly.
- [ ] Manual fulfillment path remains valid.
- [ ] Provider failure does not close Service Request automatically.
- [ ] Load/failure/restore evidence required by target launch is recorded.

### Metric definitions
- [ ] Confirmed fulfillment means canonical Request `CONFIRMED`, not merely provider/Fulfillment `COMPLETED`.
- [ ] Completion awaiting confirmation is reported separately.
- [ ] Open work is handled explicitly in rate denominators/cohorts.
- [ ] Referral completion is not Fulfillment.
- [ ] Provider/FulfillmentAttempt/manual fallback metrics remain operational, not quality/clinical outcome claims.
- [ ] Every rate has explicit numerator, denominator, cohort/window, state treatment, tenant scope, and source.

### Forbidden claims
- [ ] No suicides-prevented, lives-saved, clinical-efficacy, suicide-prediction, diagnosis/recovery, or unsupported causal health metric.
- [ ] Traction/registrations/throughput are not presented as evidence of health efficacy.

### Reporting privacy
- [ ] D-025 owns production small-cell/sensitive aggregate suppression/aggregation policy and threshold.
- [ ] No invented `<5` or other threshold is treated as canonical.
- [ ] Affected production report slices remain unavailable until D-025 closes or a safe explicit alternative is accepted.

---

## 3. Release interaction

The pilot cannot start merely because SPEC-014 is accepted. It still requires SPEC-016 released specs, SPEC-017 implementation conformance, and SPEC-018 launch readiness with all launch-required decisions/evidence.

---

## 4. Non-goals

No silent cohort expansion, waitlist-as-enrollment, clinical inference, causal outcome claim, provider-selection shortcut, or invented reporting privacy threshold.

---

## 5. Exit criteria

SPEC-014 may be accepted after dependencies when pilot scope, traction boundary, provider/operations requirements, metric definitions, forbidden claims, and D-025 reporting-privacy boundary are reviewed and aligned.

**Implementation handoff:** none until SPEC-016 release.
