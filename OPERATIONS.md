# OPERATIONS.md — Day-2 operations (SUAS v0.1)

**Related:** [PILOT.md](PILOT.md), [DEPLOYMENT.md](DEPLOYMENT.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [RESOURCES.md](RESOURCES.md), [FOLLOWUP.md](FOLLOWUP.md), [ADMIN.md](ADMIN.md)

**Status:** `draft` / `0.1.0`.

---

## 1. Purpose

Separate **veteran-support operations** from **application/platform reliability**. Both are required for launch readiness.

Coverage hours and staffing model remain D-009 `DECISION_PENDING`. This file defines duties and observability, not invented shift calendars or 24/7 claims.

---

## 2. Veteran-support operations

| Duty | Owner | Cadence | Spec |
|---|---|---|---|
| Responder coverage | Responders with `active_for_queue` | Per D-009 | [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md) |
| Queue review | Responders / Org-admin | During coverage | [CASES.md](CASES.md) |
| Resource verification | Org-admin / designated responder | Maintain freshness | [RESOURCES.md](RESOURCES.md) |
| Provider/manual-path verification | Org-admin / provider operations | Before enabling and periodically | [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md) |
| Overdue follow-up | Responsible responder; escalate to org-admin | During coverage / scheduled review | [FOLLOWUP.md](FOLLOWUP.md) |
| Failed/unknown fulfillment review | Responder / provider operations | Promptly while actionable | [FULFILLMENT.md](FULFILLMENT.md), [RESILIENCE.md](RESILIENCE.md) |
| User support | Org-admin / SUAS-admin | As needed | [AUTH.md](AUTH.md) |
| Access reviews | Org-admin / SUAS-admin | Periodic; interval pending | [AUTH.md](AUTH.md), [ADMIN.md](ADMIN.md) |
| Safety/support escalation | Responder → Org-admin → SUAS-admin | As needed | [SAFETY.md](SAFETY.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md) |

Pilot operations never bypass Consent.

---

## 3. Application reliability operations

Operations must be able to observe and act on at least:

| Duty / signal | Owner | Required action |
|---|---|---|
| App/API health, latency, error rate | Platform operator | Investigate threshold breach per D-023/runbook |
| DB pool/query health | Platform operator | Investigate saturation/slow queries |
| Durable queue depth/oldest age | Platform operator | Scale workers, identify poison/downstream bottleneck |
| Dead-letter/quarantined work | Platform operator + domain owner | Inspect; replay only when safe/authorized/audited |
| Worker failure/restart | Platform operator | Verify work returns to durable visibility |
| Notification provider outage | Platform operator | Mark degraded, backoff, use alternative channel only if independently authorized |
| Fulfillment adapter health/rate limit | Platform/provider operator | Circuit/reroute/manual fallback per policy |
| `PROVIDER_UNKNOWN` attempts | Responder/provider operator | Reconcile before risky duplicate mutation |
| Webhook auth/dedup failures | Platform/security operator | Investigate attack/configuration/integration failure |
| Audit/event append health | Platform operator | Treat write-path failure as high priority |
| Tenant noisy-neighbor signals | Platform operator | Apply approved tenant-aware backpressure/rate controls |
| Backup/restore | Platform operator | Execute schedule and periodic restore test |
| Environment isolation | Platform/security operator | No production data/secrets in non-prod |

Exact providers/products remain open decisions.

---

## 4. Provider operations

For each enabled external adapter, operations must know:

- adapter/capability;
- enabled tenant/coverage scope;
- current health state;
- contact/escalation path if applicable;
- timeout/retry/rate-limit configuration;
- whether alternate adapter exists;
- whether Manual Adapter path is available;
- how to inspect/reconcile Fulfillment Attempts;
- webhook/credential rotation procedure;
- disable/kill-switch procedure that does not corrupt open Service Requests.

A provider outage does not equal Service Request closure.

---

## 5. Scale operations

Capacity is monitored using workload dimensions in [SCALING.md](SCALING.md), including request rate, active sessions, queue age/depth, worker saturation, DB utilization, provider latency/rate limits, notification throughput, webhook lag, and audit/event growth.

Scaling response order should prefer:

1. identify bottleneck;
2. stop cascading work/backpressure if needed;
3. add stateless app/worker capacity where appropriate;
4. fix query/index/pooling issue;
5. tune adapter concurrency/rate limits;
6. consider cache/read replica/partitioning/extraction only with evidence and spec authority.

Do not default to architecture rewrites under load.

---

## 6. Escalation

1. Responder cannot fulfill / provider path fails → keep Service Request actionable; reroute/manual path or documented escalation.
2. Ambiguous external booking/dispatch → `PROVIDER_UNKNOWN`; reconcile before another risky request.
3. Safety red-state without available responder → follow [SAFETY.md](SAFETY.md); surface approved resources; do not auto-dispatch emergency services.
4. Technical outage → [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md) technical track.
5. Queue/provider degradation threatening support timeliness → platform + veteran-support operations coordinate; do not hide backlog.

---

## 7. Runbooks required before launch

At minimum:

- responder queue/coverage;
- resource verification;
- provider enable/disable and manual fallback;
- provider unknown-outcome reconciliation;
- notification outage;
- queue backlog/dead-letter replay;
- DB/app/worker outage;
- webhook failure/security issue;
- backup/restore;
- deploy rollback/forward-fix;
- cross-tenant/security incident;
- overdue Follow-Up recovery.

Runbooks use synthetic examples and do not embed secrets.

---

## 8. Testability / OPERATIONS gate

`OPERATIONS = READY` only when:

- required runbooks exist and have owners;
- D-009 staffing/coverage required for target launch is decided or explicit operating limitation is accepted;
- queue/provider/DB/app health is observable;
- dead-letter and provider unknown-outcome paths can be operated in staging;
- Manual Adapter fallback is demonstrated for enabled MVP capabilities where applicable;
- backup/restore is recorded;
- scale/resilience drills have an operator response path;
- no 24/7 or provider availability claim exceeds actual staffing/contracts.

---

## 9. Non-goals

- Equating uptime with veteran outcomes
- Invented 24/7 coverage
- Hiding backlog or provider failure to make metrics look healthy
- Using Case Notes as Follow-Up or provider reconciliation system
- Assuming external APIs eliminate human operations
