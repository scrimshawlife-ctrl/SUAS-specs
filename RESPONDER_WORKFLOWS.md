# RESPONDER_WORKFLOWS.md — Responder coordination console (SUAS v0.1)

**Related:** [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [REFERRALS.md](REFERRALS.md), [FOLLOWUP.md](FOLLOWUP.md), [AUTH.md](AUTH.md), [CONSENT.md](CONSENT.md), [API.md](API.md), [EVENT_MODEL.md](EVENT_MODEL.md)

**Actors:** Responder, Organization Administrator (assign/queue), SUAS System Administrator (audit).

---

## 1. Purpose

Define the **named actions** a Responder may take. The responder surface is a **coordination console, not an EHR**.

---

## 2. Named actions

| Action | Effect | Prerequisites |
|---|---|---|
| `CLAIM_CASE` | Creates active CaseAssignment for the actor; case → `ASSIGNED` (from `OPEN` or `TRIAGED`) | Actor `RESPONDER`, membership ACTIVE, `active_for_queue`, case in tenant, case unassigned |
| `ASSIGN_CASE` | Creates CaseAssignment for a target responder; case → `ASSIGNED` | Actor org-admin or assigned responder (reassign); target membership ACTIVE |
| `CONTACT_ATTEMPT` | Creates a Contact Attempt via `POST /cases/{id}/commands/log-contact-attempt`. Required fields: `at`, `channel`, `outcome`, `actor_id`. Emits `RESPONDER_CONTACT_LOGGED`. | Active assignment |
| `CONTACT_COMPLETE` | Completes a contact via `POST /cases/{id}/commands/complete-contact`. Required fields: `at`, `channel`, `outcome`, `actor_id`. `outcome` must not be `PENDING`. Emits `RESPONDER_CONTACT_LOGGED`. | Active assignment; prior or simultaneous attempt |
| `ADD_NOTE` | Creates CaseNote. **Not** a Contact Attempt. Must not emit `RESPONDER_CONTACT_LOGGED`. | Active assignment (or org-admin read/write policy) |
| `CREATE_REFERRAL` | Drafts/sends Referral | Active assignment; consent at send |
| `UPDATE_REFERRAL` | Updates Referral status/result | Active assignment or destination role |
| `REQUEST_SERVICE` | Creates Service Request on the case | Active assignment or veteran-initiated then responder submit |
| `SCHEDULE_FOLLOWUP` | Creates Follow-Up | Active assignment |
| `ESCALATE` | Case and/or request → escalated path | Active assignment; reason required; emit `CASE_ESCALATED` |
| `RESOLVE` | Case → `RESOLVED` | Settlement present; blocking follow-ups and requests in allowed terminal states |

No other privileged write actions are implied. Resource verification may be a separate org permission ([RESOURCES.md](RESOURCES.md)).

---

## 3. Queue filters

Responders with `active_for_queue` see cases in their Organization tenant with filters:

- Status (`OPEN`, `TRIAGED`, `ASSIGNED`, `ACTIVE`, `FOLLOWUP`, `RESOLVED`)
- `priority_signal_level` (`GREEN`/`YELLOW`/`ORANGE`/`RED`) — red-state first per [SAFETY.md](SAFETY.md)
- Unassigned vs mine
- Overdue Follow-Up present
- Category of open Service Requests
- Stale linked resources

Cross-tenant queues are forbidden.

---

## 4. Authorization

- Least privilege. Revoked membership ends actions immediately ([AUTH.md](AUTH.md)).
- Viewing check-in answers or location requires grant or documented assigned-responder basis ([CONSENT.md](CONSENT.md)).
- Critical suite: **responder authorization** ([TESTING.md](TESTING.md)).

---

## 4.1 Contact log

Command style matches [API.md](API.md) `/commands/`:

- `POST /cases/{id}/commands/log-contact-attempt`
- `POST /cases/{id}/commands/complete-contact`

A Case Note is not a substitute. Contact Attempts are first-class rows ([DATA_MODEL.md](DATA_MODEL.md)) plus `RESPONDER_CONTACT_LOGGED` ([EVENT_MODEL.md](EVENT_MODEL.md)). Veterans cannot read the contact log (D-015).

---

## 5. Events

`CASE_ASSIGNED`, `RESPONDER_CONTACT_LOGGED`, `CASE_ESCALATED`, `CASE_RESOLVED`, plus request/referral/follow-up events from those actions.

---

## 6. Non-goals

- Clinical documentation workflows
- Order entry
- Unnamed "do everything" action
- Auto-claim by a model

---

## 7. Testability

- Each action fails without prerequisites.
- `RESOLVE` without Settlement fails.
- Queue does not show other tenants.
- `CLAIM_CASE` on an assigned case fails.
- `CONTACT_ATTEMPT` / `CONTACT_COMPLETE` without `at`, `channel`, `outcome`, `actor_id` fail.
- Creating a Case Note does not create a Contact Attempt and does not emit `RESPONDER_CONTACT_LOGGED`.
- Veteran `GET` of contact-attempt rows fails (403 or empty-hidden 404).
