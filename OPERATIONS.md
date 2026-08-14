# OPERATIONS.md — Day-2 operations (SUAS v0.1)

**Related:** [PILOT.md](PILOT.md), [DEPLOYMENT.md](DEPLOYMENT.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [RESOURCES.md](RESOURCES.md), [FOLLOWUP.md](FOLLOWUP.md), [ADMIN.md](ADMIN.md)

---

## 1. Purpose

Separate **application reliability** from **veteran-support operations**. Both are required for the OPERATIONS MVP gate ([STATUS.md](STATUS.md), [TESTING.md](TESTING.md)).

Coverage hours and staffing model: D-009 `DECISION_PENDING`. This file defines the duties, not invented shift calendars.

---

## 2. Veteran-support operations

| Duty | Owner (role) | Cadence | Spec |
|---|---|---|---|
| Coverage | Responders with `active_for_queue` | Per D-009 | [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md) |
| Queue review | Responders / Org-admin | During coverage | [CASES.md](CASES.md) |
| Resource verification | Org-admin / designated responder | Keep ACTIVE resources in freshness bands | [RESOURCES.md](RESOURCES.md) |
| Overdue follow-up | Responsible responder; escalate to org-admin | Daily during coverage | [FOLLOWUP.md](FOLLOWUP.md) |
| User support (veteran how-to) | Org-admin / SUAS-admin | As needed | [AUTH.md](AUTH.md) |
| Access reviews | Org-admin (org), SUAS-admin (global) | Periodic (`DECISION_PENDING` interval) | [AUTH.md](AUTH.md), [ADMIN.md](ADMIN.md) |
| Incident escalation (support/safety) | Responder → Org-admin → SUAS-admin | Immediate when needed | [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [SAFETY.md](SAFETY.md) |

Pilot operations do not bypass consent ([PILOT.md](PILOT.md)).

---

## 3. Application reliability operations

| Duty | Owner | Cadence |
|---|---|---|
| Backups | SUAS-admin / platform operator | Documented; interval `DECISION_PENDING` |
| Restore testing | SUAS-admin | Before SPEC-014 and periodically after |
| Notification failures | SUAS-admin | On `UNDELIVERABLE` / provider outage |
| Audit health | SUAS-admin | Verify event append path is writable |
| Environment isolation | SUAS-admin | Continuous: no prod data in non-prod |

Platform vendor is `DECISION_PENDING` ([DEPLOYMENT.md](DEPLOYMENT.md)).

---

## 4. Escalation

1. Responder cannot fulfill or contacts fail → `ESCALATE` on case/request.
2. Safety red-state without available responder → follow [SAFETY.md](SAFETY.md) (surface resources, do not auto-dispatch) and page the on-call path once D-009 defines it.
3. Technical outage → [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md) technical track.

---

## 5. Non-goals

- Equating uptime with veteran outcomes
- Invented 24/7 claims while D-009 is open
- Using notes as the overdue-follow-up system

---

## 6. Testability

OPERATIONS gate: runbooks exist for each duty row; overdue follow-up job + queue filter demonstrated in STAGING; backup/restore recorded.
