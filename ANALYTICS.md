# ANALYTICS.md — Operational metrics only (SUAS v0.1)

**Related:** [STATUS.md](STATUS.md), [PILOT.md](PILOT.md), [TESTING.md](TESTING.md), [RESOURCES.md](RESOURCES.md), [FOLLOWUP.md](FOLLOWUP.md)

---

## 1. Purpose

Define **operational** metrics for the pilot and for the REPORTING MVP gate. SUAS is not a clinical-efficacy product.

---

## 2. Allowed metrics

| Metric | Definition (logical) |
|---|---|
| Enrollment | Count of PilotEnrollment in `ENROLLED` |
| Check-in completion | Count/rate of Check-Ins in `COMPLETED` among started |
| Time to assignment | `CASE_ASSIGNED.occurred_at - CASE_CREATED.occurred_at` (median/p50, p90) |
| Time to first contact | First `RESPONDER_CONTACT_LOGGED` minus `CASE_CREATED` |
| Fulfillment rate | Service Requests reaching `CONFIRMED` or fulfillment `COMPLETED`/`CONFIRMED` over submitted |
| Median fulfillment time | `SERVICE_FULFILLED.occurred_at - SERVICE_REQUEST_CREATED.occurred_at` |
| Follow-up completion | Follow-Ups `COMPLETED` over created (excluding `CANCELLED`) |
| Referral completion | Referrals in `COMPLETED` over `SENT` |
| Trusted-circle adoption | Veterans with >=1 Trusted Contact `ACCEPTED` over enrolled |
| Consent revocations | Count of `CONSENT_REVOKED` |
| Resource freshness | Share of ACTIVE resources in <30 / 30-90 / >90 day bands |
| Responder workload | Open assigned cases per responder; contacts per shift (shift definition D-009) |
| Veteran satisfaction | From Feedback scores when collected |
| Responder satisfaction | From Feedback scores when collected |

Exact dashboard product is `DECISION_PENDING`. The **names and meanings** above are the contract.

---

## 3. Forbidden product metrics

Do **not** implement, display, or report as product metrics:

- Suicides prevented
- Clinical efficacy
- Suicide-prediction accuracy
- Lives saved
- Diagnosis rates

These are not computable from SUAS data and are out of product scope.

---

## 4. Rules

- Aggregates only in org-admin and SUAS-admin reports unless a metric is the veteran's own counts.
- No re-identification via tiny cells: if a slice has < 5 veterans, suppress (`INFERRED` operational default).
- Metrics use Domain Events and operational tables, not notes text mining.

---

## 5. REPORTING gate

[STATUS.md](STATUS.md) REPORTING = ability to produce the allowed metrics and the **absence** of forbidden metrics.

---

## 6. Testability

- Fixture loop produces each allowed metric.
- Test fails if a forbidden metric name appears in the reporting API.
