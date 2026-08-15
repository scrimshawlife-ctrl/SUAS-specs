# CASES.md — Support Case state machine (SUAS v0.1)

**Related:** [DISPATCH.md](DISPATCH.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [FOLLOWUP.md](FOLLOWUP.md), [SETTLEMENT.md](SETTLEMENT.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [EVENT_MODEL.md](EVENT_MODEL.md), [API.md](API.md), [DECISIONS.md](DECISIONS.md)

**Actors:** System, Responder, Organization Administrator (queue), Veteran (limited), SUAS System Administrator (audit / break-glass).

---

## 1. Purpose

A **Support Case** is coordination around a Veteran. It is not a Service Request. One case may contain multiple Service Requests.

---

## 2. States

Exactly:

`OPEN` → `TRIAGED` → `ASSIGNED` → `ACTIVE` → `FOLLOWUP` → `RESOLVED` → `CLOSED`

| State | Meaning |
|---|---|
| `OPEN` | Case exists; not yet triaged |
| `TRIAGED` | Need/priority reviewed; not yet assigned |
| `ASSIGNED` | An active CaseAssignment exists; work may not have started |
| `ACTIVE` | Responder is working the case (contact, requests, referrals) |
| `FOLLOWUP` | Primary coordination actions done or paused; Follow-Up records remain due |
| `RESOLVED` | Settlement drafted/accepted; no open blocking work |
| `CLOSED` | Terminal. History retained. Not a delete |

Returns and skips are allowed only as documented in §4. No hidden transitions.

---

## 3. Creation

A case is created when:

1. An effective Support Signal is `YELLOW`, `ORANGE`, or `RED` and no non-closed case exists for that veteran that operations policy says should absorb the signal (MVP: one active case per veteran — `INFERRED` operational default; multiple concurrent cases are `FUTURE` unless later specified), or
2. A Veteran or Responder explicitly opens a case for a stated need (including `GREEN` if a concrete Service Request is created).

Emit `CASE_CREATED`. Set `priority_signal_level` from the effective signal when applicable.

Red-state creation/priority: [SAFETY.md](SAFETY.md).

---

## 4. Transitions

Every transition records: source, target, actor, prerequisites, Domain Event, timestamps, notifications (if any).

| Source | Target | Actor | Prerequisites | Event |
|---|---|---|---|---|
| (none) | `OPEN` | System or Responder or Veteran | Veteran enrolled | `CASE_CREATED` |
| `OPEN` | `TRIAGED` | Responder | Actor authorized | (audit; optional domain note) |
| `TRIAGED` | `ASSIGNED` | Responder or Org-admin | Target responder membership ACTIVE | `CASE_ASSIGNED` |
| `OPEN` | `ASSIGNED` | Responder (`CLAIM_CASE`) or Org-admin | Same | `CASE_ASSIGNED` |
| `ASSIGNED` | `ACTIVE` | Assigned Responder | First qualifying work action (contact attempt, request, referral, note-with-work flag) | (audit) |
| `ACTIVE` | `FOLLOWUP` | Assigned Responder | At least one Follow-Up scheduled or explicit command | `FOLLOWUP_CREATED` if new |
| `ACTIVE` | `RESOLVED` | Assigned Responder | No open Service Requests in non-terminal states except those marked unfulfillable/cancelled; Settlement present | `CASE_RESOLVED` |
| `FOLLOWUP` | `RESOLVED` | Assigned Responder | Blocking follow-ups completed or cancelled; Settlement present | `CASE_RESOLVED` |
| `FOLLOWUP` | `ACTIVE` | Assigned Responder | New work needed | (audit) |
| `RESOLVED` | `CLOSED` | Assigned Responder or Org-admin | Settlement recorded | (audit) |
| `ASSIGNED` or `ACTIVE` | `ASSIGNED` | Org-admin or assigned Responder | Reassignment: release old, create new CaseAssignment | `CASE_ASSIGNED` |
| any non-terminal except `CLOSED` | `ACTIVE` (escalation path) | Assigned Responder | `ESCALATE` action | `CASE_ESCALATED` |

`CLOSED` has no outbound transition except a documented reopen: `CLOSED` → `OPEN` by assigned-org responder or SUAS-admin with reason (audit). Reopen does not delete prior Settlement; a new Settlement will be required to resolve again.

---

## 5. Assignment, reassignment, ownership

- **Assignment** = `CaseAssignment` with `status=ACTIVE`. Assignment is not Fulfillment.
- Claim (`CLAIM_CASE`) and assign (`ASSIGN_CASE`) are defined in [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md).
- Reassignment sets prior assignment `REASSIGNED` or `RELEASED`.
- Queue: unassigned `OPEN`/`TRIAGED` cases visible to responders with `active_for_queue` in the same Organization tenant. Cross-org queue is `FUTURE` / not specified.

---

## 6. Notes, linked requests, follow-ups

- Case Notes are not transitions, not Follow-Ups, and not Contact Attempts.
- Contact Attempts use `log-contact-attempt` / `complete-contact` ([API.md](API.md) section 11.1). A Case Note is not a substitute.
- Service Requests live on the case ([DISPATCH.md](DISPATCH.md)).
- Follow-Ups are first-class ([FOLLOWUP.md](FOLLOWUP.md)).
- Escalation is an explicit action, not a note keyword.

---

## 7. Resolution and closure

- **Resolved** requires a Settlement ([SETTLEMENT.md](SETTLEMENT.md)).
- **Closed** retains all history. Closure does not delete notes, requests, events, or signals.
- Case closure is safety-critical in the sense that a generative model must not determine it ([SAFETY.md](SAFETY.md)).

---

## 8. Authorization

- Assigned responder: read/write per workflows.
- Org queue responders: read limited fields of unassigned cases in tenant.
- Trusted Contact: only with grants; membership insufficient.

### 8.1 Veteran visibility (MVP default, `INFERRED`; D-015 remains open)

This is an operational default so implementation does not invent a clinical chart. D-015 stays open if the owner later wants veterans to see notes.

**Veteran can see (own records only):**

- own Check-Ins
- own Service Request status
- Settlement fields written for them
- Follow-Up prompts addressed to them
- Support Case existence and status on their own case

**Veteran cannot see:**

- full Case Notes
- Contact Attempts / the responder contact log
- other veterans
- responder internal queue fields (filters, unassigned queue, `active_for_queue` internals, other responders' assignments)
- other Organizations / other tenants

Do not invent a clinical chart. Case Notes remain responder/org-admin/SUAS-admin artifacts.

---

## 9. Non-goals

- EHR charting
- Hidden status values
- Deleting a case to "clean up"
- Auto-close on signal returning to `GREEN`

---

## 10. Testability

Critical suite: **case transitions**.

- Only documented edges succeed.
- Closure retains history.
- Reassignment emits `CASE_ASSIGNED` and releases the prior assignment.
- Resolve without Settlement fails.
- Veteran cannot read Case Notes or Contact Attempts (D-015 MVP default).
