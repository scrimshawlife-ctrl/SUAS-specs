# DISPATCH.md — Service Request state machine (SUAS v0.1)

**Related:** [CASES.md](CASES.md), [FULFILLMENT.md](FULFILLMENT.md), [RESOURCES.md](RESOURCES.md), [REFERRALS.md](REFERRALS.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [EVENT_MODEL.md](EVENT_MODEL.md)

**Actors:** Veteran (request), Responder (triage/match/assign), Service Provider (accept/decline), System (expiry jobs).

---

## 1. Purpose

A **Service Request** is a specific requested need. It is not a Support Case, not a Referral, and not Fulfillment. Dispatch is the documented transition set for that request.

MVP matching is **responder-selected** from the Resource catalog. There is no generative matcher and no unspecified "smart matching."

---

## 2. Happy-path states

`CREATED` → `SUBMITTED` → `TRIAGED` → `MATCHING` → `ASSIGNED` → `ACCEPTED` → `IN_PROGRESS` → `FULFILLED` → `CONFIRMED` → `CLOSED`

## 3. Exception states

`CANCELLED` | `DECLINED` | `EXPIRED` | `UNFULFILLABLE` | `ESCALATED`

Exception states are terminal for the current attempt except `ESCALATED` (returns to `TRIAGED` or `MATCHING` after human action) and `DECLINED` (may return to `MATCHING` for a new provider). Each return is a documented transition, not an implicit reset.

---

## 4. Transitions

Every transition records: source, target, actor, prerequisites, event, timestamps, notifications. **No hidden transitions.**

| Source | Target | Actor | Prerequisites | Event |
|---|---|---|---|---|
| (none) | `CREATED` | Veteran or Responder | Parent Support Case exists and is not `CLOSED` | `SERVICE_REQUEST_CREATED` |
| `CREATED` | `SUBMITTED` | Same or assigned Responder | Category in MVP set; required details present | (audit) |
| `SUBMITTED` | `TRIAGED` | Responder | Case assigned or claim | (audit) |
| `TRIAGED` | `MATCHING` | Responder | — | (audit) |
| `MATCHING` | `ASSIGNED` | Responder | Resource or Service Provider selected; consent if disclosing veteran data to provider | `SERVICE_REQUEST_ASSIGNED` |
| `ASSIGNED` | `ACCEPTED` | Assigned provider or Responder-on-behalf with reason | Provider membership ACTIVE | `SERVICE_ACCEPTED` |
| `ACCEPTED` | `IN_PROGRESS` | Provider or Responder | Fulfillment `STARTED` | (audit) |
| `IN_PROGRESS` | `FULFILLED` | Provider or Responder | Fulfillment `COMPLETED` — **not** merely assigned | `SERVICE_FULFILLED` |
| `FULFILLED` | `CONFIRMED` | Veteran and/or Responder per [FULFILLMENT.md](FULFILLMENT.md) | Confirmation recorded | (audit) |
| `CONFIRMED` | `CLOSED` | Responder | — | (audit) |
| `ASSIGNED` | `DECLINED` | Provider | Reason required | (audit) |
| `DECLINED` | `MATCHING` | Responder | — | (audit) |
| any non-terminal | `CANCELLED` | Veteran or Responder | Reason | (audit) |
| `CREATED`/`SUBMITTED`/`TRIAGED`/`MATCHING`/`ASSIGNED` | `EXPIRED` | System | Documented TTL elapsed (`DECISION_PENDING` exact TTL) | (audit) |
| `TRIAGED`/`MATCHING`/`ASSIGNED`/`DECLINED` | `UNFULFILLABLE` | Responder | Reason; no acceptable resource | (audit) |
| any non-terminal except `CLOSED` | `ESCALATED` | Responder | `ESCALATE` | (audit; may emit `CASE_ESCALATED`) |
| `ESCALATED` | `TRIAGED` or `MATCHING` | Responder | After escalation review | (audit) |

`FULFILLED` without confirmation is not `CONFIRMED`. Assignment is never `FULFILLED`.

---

## 5. Categories

MVP: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.  
Reserved future: `BENEFITS`, `HOUSING`, `HEALTHCARE_NAVIGATION`, `COMMUNITY`, `OTHER`. Reject unknown codes.

---

## 6. Consent

Assigning a provider who will receive veteran data requires an `ACTIVE` grant or the documented assigned-provider basis covering the fields sent. See [CONSENT.md](CONSENT.md).

---

## 7. Notifications

On `ASSIGNED`, `ACCEPTED`, `DECLINED`, `FULFILLED`, `CANCELLED`, `EXPIRED`, `ESCALATED` — notify parties who have channel prefs **and** consent/basis. Templates contain no safety-critical branching.

---

## 8. Non-goals

- Hidden transitions
- Auto-fulfill on assign
- Generative matching
- Treating Referral send as a Service Request transition

---

## 9. Testability

Critical suite: **service-request transitions**.

- Every illegal edge fails.
- `ASSIGNED` ≠ `FULFILLED`.
- Confirm requires fulfillment completion.
- Expired job only fires from listed sources.
