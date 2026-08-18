# RESPONDER_WORKFLOWS.md — Responder coordination console (SUAS v0.1)

**Status:** `draft` / `0.1.0` / SPEC-004 preflight; not implementation authority.  
**Related:** [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [REFERRALS.md](REFERRALS.md), [FOLLOWUP.md](FOLLOWUP.md), [AUTH.md](AUTH.md), [CONSENT.md](CONSENT.md), [API.md](API.md), [EVENT_MODEL.md](EVENT_MODEL.md), [MVP_REFERENCE.md](MVP_REFERENCE.md), [SCALING.md](SCALING.md)

**Actors:** Responder, Organization Administrator, SUAS System Administrator (audited admin/break-glass paths only).

---

## 1. Purpose

Define the named Responder coordination actions and queue semantics. The responder/QRF surface is a **coordination console, not an EHR** and must preserve the action-first/operational immediacy of the referenced MVP.

---

## 2. Named actions

| Action | Effect | Prerequisites / concurrency rule |
|---|---|---|
| `CLAIM_CASE` | Atomically creates active CaseAssignment for actor; `OPEN`/`TRIAGED` → `ASSIGNED` | RESPONDER, ACTIVE membership, `active_for_queue`, same tenant, case unassigned at mutation time; one contender wins |
| `ASSIGN_CASE` | Atomically creates/replaces CaseAssignment | Org-admin or authorized assigned responder for reassignment; target membership ACTIVE; stale assignment conflict protected |
| `CONTACT_ATTEMPT` | Creates Contact Attempt; emits `RESPONDER_CONTACT_LOGGED` | Active assignment; required `at`, `channel`, `outcome`, `actor_id` |
| `CONTACT_COMPLETE` | Completes/records contact; emits `RESPONDER_CONTACT_LOGGED` | Active assignment; `outcome != PENDING` |
| `ADD_NOTE` | Creates CaseNote | Active assignment or accepted org-admin policy; never a Contact Attempt/transition |
| `CREATE_REFERRAL` | Drafts/sends Referral | Active assignment; consent at send |
| `UPDATE_REFERRAL` | Updates Referral status/result | Active assignment or authorized destination role |
| `REQUEST_SERVICE` | Creates Service Request | Active assignment, or veteran-initiated path followed by authorized responder handling |
| `SCHEDULE_FOLLOWUP` | Creates Follow-Up | Active assignment |
| `ESCALATE` | Executes documented Case and/or Service Request escalation | Active assignment; reason; only edges allowed by CASES/DISPATCH |
| `RESOLVE` | Case → `RESOLVED` | Active assignment; Settlement present; blocking work satisfies contract |

No other privileged write actions are implied.

---

## 3. Command idempotency and stale-state protection

All responder state-changing commands use the [API.md](API.md) idempotency contract.

Required behavior:

1. Mutation-time state/assignment checks are authoritative; queue state shown to the user may already be stale.
2. Replaying the same logical command returns the original result rather than creating another assignment/note/contact transition where the command is defined as idempotent.
3. Concurrent incompatible commands yield one valid winner; losers receive a conflict and no partial event/effect.
4. UI may optimistically disable an action after click, but correctness must not depend on the browser preventing double submit.
5. A conflict response should return enough non-sensitive current-state information for the console to refresh rather than presenting a generic failure loop.

---

## 4. Queue contract

Responders with `active_for_queue` may query same-tenant Cases using bounded/paginated filters such as:

- status (`OPEN`, `TRIAGED`, `ASSIGNED`, `ACTIVE`, `FOLLOWUP`, `RESOLVED`);
- `priority_signal_level` (`GREEN`/`YELLOW`/`ORANGE`/`RED`);
- unassigned vs mine;
- overdue Follow-Up present;
- category of open Service Requests;
- stale linked resources.

Rules:

1. Cross-tenant queue results are forbidden.
2. Queue APIs use cursor/limit or equivalent bounded pagination; no full-queue browser load.
3. Red/high-priority sorting is a view/order rule, not an ownership lock.
4. Queue result freshness is advisory. `CLAIM_CASE` re-checks eligibility/assignment atomically.
5. One tenant's large queue must not require reading/scanning unrelated tenants.
6. Refresh/retry after a claim conflict must preserve the user's filters and avoid cognitive overload.

---

## 5. `CLAIM_CASE` UX/behavior

The referenced MVP/QRF model values immediate response. Production preserves that speed while making claim semantics safe.

Expected interaction:

```text
Responder sees unassigned need
        ↓
CLAIM_CASE
        ↓
atomic server decision
   ├─ win → case becomes mine / ASSIGNED
   └─ conflict → show already-claimed/current state, refresh queue
```

Do not use long pessimistic browser locks. Do not show a case as successfully claimed until the server commits the assignment.

---

## 6. Authorization

- Revoked/suspended membership ends actions immediately.
- Check-In answers/location require applicable grant or documented assigned-responder basis.
- Provider disclosures from responder actions use consent/minimum-necessary rules.
- Org-admin queue scope remains organization/tenant-bound unless a separately accepted cross-org model exists.
- SUAS-admin does not silently act as ordinary Responder; break-glass/admin workflow is audited.

---

## 7. Contact log

Commands:

- `POST /cases/{id}/commands/log-contact-attempt`
- `POST /cases/{id}/commands/complete-contact`

A Case Note is not a substitute. Contact Attempts are first-class rows and emit `RESPONDER_CONTACT_LOGGED`.

Duplicate command replay must not create duplicate logical contact facts when the same idempotency key is reused.

Veterans cannot read the responder contact log under the MVP D-015 default.

---

## 8. Escalation

`ESCALATE` follows [CASES.md](CASES.md) and [DISPATCH.md](DISPATCH.md); it is not a wildcard transition.

- unassigned `OPEN`/`TRIAGED` Case: responder cannot use an assigned-responder escalation edge; priority/assignment workflow applies;
- `ASSIGNED`/`ACTIVE`/`FOLLOWUP`: assigned responder may perform the documented Case escalation path;
- Service Request escalation uses only its documented non-terminal edges;
- reason is required and audit/event semantics apply.

---

## 9. Events

Named responder actions may emit:

- `CASE_ASSIGNED`
- `RESPONDER_CONTACT_LOGGED`
- `CASE_ESCALATED`
- `CASE_RESOLVED`
- request/referral/follow-up events defined by owning specs.

Duplicate command delivery does not duplicate the logical event.

---

## 10. MVP visual/interaction requirements

The responder console must preserve the referenced MVP's emphasis on:

- availability/on-duty state;
- urgent/current needs;
- fast claim/contact/escalate actions;
- obvious ownership (unassigned vs mine);
- quick resource/service access;
- communication visibility;
- low navigation depth on mobile.

Production concurrency/error states must be understandable without turning the surface into a dense enterprise case-management UI.

Detailed visual acceptance remains [MVP_REFERENCE.md](MVP_REFERENCE.md) / SPEC-008.

---

## 11. Non-goals

- clinical documentation/order-entry workflows;
- unnamed do-everything action;
- model auto-claim;
- browser lock as concurrency control;
- unbounded queue result;
- cross-tenant queue;
- treating stale queue state as mutation authority;
- silent automatic retry of a claim after another responder has won.

---

## 12. Testability

- each action fails without prerequisites;
- concurrent `CLAIM_CASE` has exactly one winner;
- claim replay with same idempotency identity returns original result;
- stale queue item claim conflicts safely;
- concurrent reassignment/claim cannot create two active owners;
- queue pagination/filtering does not cross tenant;
- RESOLVE without Settlement fails;
- invalid escalation edge fails;
- Contact Attempt commands require documented fields and active assignment;
- duplicate contact command replay does not create duplicate logical contact event;
- Case Note does not emit `RESPONDER_CONTACT_LOGGED`;
- veteran contact-log read is denied/hidden.
