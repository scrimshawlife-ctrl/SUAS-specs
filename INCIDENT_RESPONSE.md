# INCIDENT_RESPONSE.md — Technical and operational/safety incidents (SUAS v0.1)

**Related:** [OPERATIONS.md](OPERATIONS.md), [SAFETY.md](SAFETY.md), [SECURITY.md](SECURITY.md), [EVENT_MODEL.md](EVENT_MODEL.md), [PILOT.md](PILOT.md)

---

## 1. Purpose

Define a single process with two tracks: **technical** (availability, integrity, unauthorized access) and **operational/safety** (veteran-support incidents, red-state without coverage, consent failures that caused a bad notify).

Do **not** invent legal notification deadlines. D-006 (legal/HIPAA classification) is open. When a deadline is legally required, it will be added in a later spec version.

---

## 2. Process

```
detect -> contain -> preserve evidence -> notify authorized owners -> remediate -> review -> close
```

| Step | Technical | Operational / safety |
|---|---|---|
| Detect | Monitoring, user report, audit anomaly | Responder report, veteran report, missed red-state review |
| Contain | Revoke sessions, disable provider, feature-flag off a path | Stop further bad notifies; do not auto-dispatch emergency services |
| Preserve evidence | Snapshot logs, freeze related Audit/Domain Events (already immutable) | Preserve case, notifications, consent rows; do not "clean up" notes |
| Notify authorized owners | SUAS-admin; org-admin if tenant-scoped; do not notify Trusted Contacts unless a grant and a documented purpose exist | Assigned responder, org-admin, SUAS-admin; veteran when appropriate |
| Remediate | Patch, rotate secrets, restore if needed | Human follow-up; correct resource; revoke bad access |
| Review | Timeline, cause, spec gap? | Same; return gaps to `SUAS-specs` |
| Close | Written record; Audit Event | Written record on the case or incident file; Audit Event |

---

## 3. Roles

- **Detector:** any user or system job
- **Incident owner:** SUAS-admin (technical) or Org-admin / SUAS-admin (safety)
- **Authorized owners:** only roles listed; not the public, not unconsented contacts

---

## 4. Evidence

Do not mutate Audit Events. Do not delete Domain Events. Exports of evidence are audited ([PRIVACY.md](PRIVACY.md)).

---

## 5. Non-goals

- Invented statutory deadlines
- Automated emergency dispatch as a "containment" step
- Public status-page vendor selection (`DECISION_PENDING`)

---

## 6. Testability

Tabletop: one technical scenario (cross-tenant bug) and one safety scenario (red-state with revoked grant almost-notified). Each step has an owner and an artifact.
