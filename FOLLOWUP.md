# FOLLOWUP.md — First-class Follow-Up (SUAS v0.1)

**Related:** [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [SETTLEMENT.md](SETTLEMENT.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md)

**Actors:** Responder (usually responsible), Veteran (when addressed), Org-admin, System (due/overdue jobs).

---

## 1. Purpose

A Follow-Up is a first-class work item. It is **not** a Case Note and **not** a Settlement.

---

## 2. States

`SCHEDULED` → `DUE` → `COMPLETED` | `RESCHEDULED` | `OVERDUE` → `ESCALATED` | `CANCELLED`

| State | Meaning |
|---|---|
| `SCHEDULED` | Future `due_at` |
| `DUE` | `due_at` reached; not completed |
| `COMPLETED` | Done; `completed_at` set |
| `RESCHEDULED` | New `due_at`; prior times remain in events |
| `OVERDUE` | Past due without completion |
| `ESCALATED` | Overdue (or manual) escalation performed |
| `CANCELLED` | Will not be done; reason required |

---

## 3. Rules

- **Due date** required.
- **Responsibility** required (`responsible_type` + `responsible_id`).
- Veteran can see Follow-Up prompts addressed to them (`INFERRED` MVP default; [CASES.md](CASES.md) section 8).
- **Retries:** `retry_count` increments on each unsuccessful contact attempt tied to this follow-up, or on each resend of a due notification. Retry bound `DECISION_PENDING` (document a constant; recommended operational default 3 — `INFERRED`).
- **Completion:** actor + timestamp. Emit `FOLLOWUP_COMPLETED`.
- **Reschedule:** reason + new `due_at`. History via events, not silent overwrite of the original due time in the event log.
- **Overdue:** job marks `OVERDUE` and notifies responsible party if consented/basis exists.
- **Escalation:** from `OVERDUE` or manual; notify org-admin or queue per [OPERATIONS.md](OPERATIONS.md); may emit `CASE_ESCALATED` if the parent case is escalated.

Parent: `support_case_id` required. Optional `service_request_id` or `referral_id` (via referral.follow_up_id).

---

## 4. Events

`FOLLOWUP_CREATED`, `FOLLOWUP_DUE`, `FOLLOWUP_COMPLETED`.

---

## 5. Case interaction

When the only remaining work is follow-up, the Support Case may move to `FOLLOWUP` ([CASES.md](CASES.md)). Completing blocking follow-ups is a prerequisite for `RESOLVED`.

---

## 6. Non-goals

- Storing follow-ups only as note text
- Auto-complete on case close without completing or cancelling each follow-up
- Clinical outcome measurement

---

## 7. Testability

- Create without `due_at` fails.
- Due job emits `FOLLOWUP_DUE`.
- Case cannot `RESOLVE` with blocking `SCHEDULED`/`DUE`/`OVERDUE` follow-ups.
- Notes cannot substitute for a Follow-Up in reporting ([ANALYTICS.md](ANALYTICS.md)).
