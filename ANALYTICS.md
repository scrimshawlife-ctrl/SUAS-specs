# ANALYTICS.md — Operational metrics only (SUAS v0.1)

**Status:** `draft` / `0.1.0`; SPEC-014 dependency-blocked.  
**Related:** [STATUS.md](STATUS.md), [PILOT.md](PILOT.md), [TESTING.md](TESTING.md), [RESOURCES.md](RESOURCES.md), [FOLLOWUP.md](FOLLOWUP.md), [FULFILLMENT.md](FULFILLMENT.md), [DECISIONS.md](DECISIONS.md)

---

## 1. Purpose

Define operational coordination metrics for the controlled pilot and REPORTING gate. SUAS does not measure clinical efficacy and must not convert operational coordination data into unsupported health/suicide-prevention claims.

---

## 2. Metric contract rules

Every reported metric must define:
- numerator/count definition;
- denominator where a rate is used;
- cohort/time window;
- terminal/open-state treatment;
- tenant/role scope;
- source fields/events;
- whether the metric is point-in-time or completed-cohort.

A dashboard label without these definitions is not a metric contract.

Do not combine different business states merely to produce a more favorable rate.

---

## 3. Allowed operational metrics

| Metric | Logical definition |
|---|---|
| Enrolled veterans | count of PilotEnrollment currently `ENROLLED` for the selected pilot/tenant/time snapshot |
| Check-In completion count | Check-Ins entering `COMPLETED` in the reporting window |
| Check-In completion rate | completed Check-Ins / Check-Ins started in a defined completed-cohort window; abandoned/incomplete treatment shown explicitly |
| Case status distribution | count of Support Cases by canonical status at snapshot/window |
| Time to Case assignment | first successful `CASE_ASSIGNED.occurred_at - CASE_CREATED.occurred_at` for Cases assigned in the cohort; percentile summary when sample permits |
| Time to first responder contact | first `RESPONDER_CONTACT_LOGGED.at - CASE_CREATED.occurred_at` for Cases with a qualifying contact |
| Service Request status distribution | count of Requests by canonical Request state |
| Confirmed fulfillment count | Service Requests reaching canonical `CONFIRMED` in the window |
| Confirmed fulfillment rate | Requests reaching `CONFIRMED` / Requests in a defined closed/terminal evaluation cohort; open Requests are reported separately rather than silently counted as failures or successes |
| Completion awaiting confirmation | ServiceFulfillments at `COMPLETED` whose Request has not yet reached `CONFIRMED`; this is **not** confirmed fulfillment |
| Time to fulfillment completion | `SERVICE_FULFILLED.occurred_at - SERVICE_REQUEST_CREATED.occurred_at` for completed fulfillment cohort |
| Time to confirmation | Request `CONFIRMED` time minus Request creation for confirmed cohort, when the canonical event/timestamp is available |
| FulfillmentAttempt outcome distribution | counts by normalized attempt status, including `PROVIDER_UNKNOWN`, manual, failed, rerouted outcomes; provider-specific status excluded |
| Manual fallback use | count/rate of Requests using Manual Adapter/coordination path in a defined cohort; operational, not quality judgment |
| Follow-Up completion | completed Follow-Ups / Follow-Ups in a defined due/closed cohort; cancelled/carried-forward treatment disclosed |
| Overdue Follow-Up backlog | unresolved Follow-Ups currently `OVERDUE`/`ESCALATED` as defined |
| Referral status/completion | Referral distribution and `COMPLETED` count/rate over a defined sent/terminal cohort; Referral completion is not Fulfillment |
| Trusted Circle adoption | enrolled veterans with ≥1 `ACCEPTED` Trusted Contact / enrolled veterans in defined snapshot; says nothing about support outcomes |
| Consent revocations | count of `CONSENT_REVOKED` in window |
| Resource freshness distribution | Resources by accepted freshness-band configuration; exact band thresholds must be reported with the metric |
| Responder workload | open assigned Cases/current responder and contact/action counts over defined coverage window; D-009 controls staffing/shift semantics |
| Queue/provider operational health | queue age/depth, failed/dead-letter work, provider health/rate-limit/reconciliation backlog as operational telemetry |
| Veteran feedback | explicitly collected satisfaction/experience fields; not clinical outcome |
| Responder feedback | explicitly collected operational satisfaction/experience fields |

Percentiles (`p50`, `p90`, etc.) may be displayed only when the report names the statistic and the cohort is large enough under the accepted reporting-privacy rule.

---

## 4. Forbidden metrics / claims

Do not implement or report as SUAS product metrics:

- suicides prevented;
- lives saved;
- clinical efficacy/effectiveness;
- suicide-prediction accuracy;
- diagnosis/recovery rates;
- causal claim that SUAS or a provider caused a health outcome;
- “QRF success rate” defined from mere notification/assignment rather than explicit canonical coordination facts.

These are not computable from the specified SUAS data/causal design.

---

## 5. Reporting privacy

Operational reporting is tenant/role scoped and must prevent inappropriate re-identification of veterans through small slices or sensitive combinations.

The **exact small-cell suppression/aggregation threshold and policy is D-025 `DECISION_PENDING`**. Do not hard-code an invented `<5` or other number as canonical policy before D-025 closes.

Until D-025 closes:
- non-production fixtures may exercise a clearly labeled test suppression value;
- production reporting cannot claim a specific privacy-preserving threshold as approved;
- raw veteran-level exports remain governed by PRIVACY/authz, not aggregate dashboard permissions.

---

## 6. Scale vs outcome separation

Traction/capacity metrics such as registrations, active sessions, queue throughput, provider calls, or response volume are operational/scale observations. They must not be presented as evidence that the product improves clinical or suicide-related outcomes.

D-021/D-023 load/SLO evidence belongs to scale/operations reporting, not product efficacy claims.

---

## 7. REPORTING gate

`REPORTING = READY` only when:
- each enabled metric has a deterministic definition/cohort/source;
- conflicting states such as `COMPLETED` vs `CONFIRMED` are not collapsed;
- forbidden metrics/claims are absent;
- D-025 reporting-privacy policy is decided for production reports or affected report slices remain unavailable;
- representative synthetic fixtures reproduce expected calculations;
- tenant/privacy authorization tests pass.

Current: `NOT_READY`.

---

## 8. Testability

Tests include:
- open Request excluded/reported separately from terminal confirmed-rate denominator according to metric definition;
- Fulfillment `COMPLETED` without Request `CONFIRMED` appears only in completion-awaiting-confirmation, not confirmed fulfillment;
- Referral `COMPLETED` never increments confirmed Fulfillment metric;
- provider-specific status strings do not appear as canonical analytics dimensions;
- multi-cycle Case Settlement/reopen does not double-count one Request unless the metric explicitly counts cycles;
- tenant filter enforced;
- forbidden metric/claim names fail reporting-contract lint/test;
- D-025 suppression policy is testable once decided.

---

## 9. Non-goals

Clinical research, causal inference from observational SUAS operations, predictive risk scoring, marketing outcome claims, or invented privacy thresholds.
