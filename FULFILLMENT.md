# FULFILLMENT.md — Service fulfillment (SUAS v0.1)

**Related:** [DISPATCH.md](DISPATCH.md), [SETTLEMENT.md](SETTLEMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [REFERRALS.md](REFERRALS.md), [EVENT_MODEL.md](EVENT_MODEL.md)

**Actors:** Service Provider, Responder, Veteran, System.

---

## 1. Purpose

Fulfillment is the record that a Service Request was accepted, started, completed, and confirmed — or failed, partial, or cancelled.

**A Service Request is not fulfilled merely because it is assigned.**

Funding is separate from fulfillment ([SETTLEMENT.md](SETTLEMENT.md)).

---

## 2. Fulfillment states

`ACCEPTED` → `STARTED` → `COMPLETED` → `CONFIRMED` | `DISPUTED`

Exceptions: `FAILED` | `PARTIAL` | `CANCELLED`

| State | Meaning |
|---|---|
| `ACCEPTED` | Provider accepted the assignment |
| `STARTED` | Work/delivery began |
| `COMPLETED` | Provider or responder marked the work done |
| `CONFIRMED` | Veteran and/or responder confirmed per §4 |
| `DISPUTED` | Veteran or responder rejected the completion claim |
| `FAILED` | Attempted; did not occur |
| `PARTIAL` | Some but not all of the requested need occurred |
| `CANCELLED` | Stopped before completion |

---

## 3. Mapping to Service Request

| Fulfillment | Service Request (see [DISPATCH.md](DISPATCH.md)) |
|---|---|
| `ACCEPTED` | `ACCEPTED` |
| `STARTED` | `IN_PROGRESS` |
| `COMPLETED` | `FULFILLED` |
| `CONFIRMED` | `CONFIRMED` |
| `FAILED` | typically `UNFULFILLABLE` or remain `IN_PROGRESS` then exception — responder chooses a documented exception |
| `PARTIAL` | `FULFILLED` with fulfillment status `PARTIAL` (request may still need a Follow-Up) |
| `CANCELLED` | `CANCELLED` |

Do not set request `FULFILLED` from `ASSIGNED` alone.

---

## 4. Confirmation

- Veteran confirmation is preferred when the veteran is reachable.
- Responder confirmation is required when the veteran cannot be reached (reason recorded) or as a second confirmation.
- MVP rule: `CONFIRMED` requires **at least one** of `veteran_confirmed_at` or `responder_confirmed_at`. If only responder confirms, reason is required.
- Dispute returns fulfillment to `DISPUTED` and the request does not move to `CONFIRMED`.

---

## 5. Failure, partial, cancellation

All three are first-class. Reason is required. A Follow-Up should be scheduled unless the parent case is moving to Settlement that documents no further follow-up.

---

## 6. Funding

No funding fields in MVP. Path:

`Fulfillment → Funding Eligibility → Funding Source → Optional Billing Adapter`

**Status:** `FUTURE`. Do not assert Medi-Cal billability. See [SETTLEMENT.md](SETTLEMENT.md).

---

## 7. Events

`SERVICE_ACCEPTED`, `SERVICE_FULFILLED`, `SERVICE_FAILED`.

---

## 8. Non-goals

- Auto-confirm on timer without a recorded actor (timer may nudge a Follow-Up only)
- Treating Referral `COMPLETED` as ServiceFulfillment
- Billing

---

## 9. Testability

- Assigned request cannot be `FULFILLED` without a fulfillment completion.
- Confirm without veteran or responder timestamp fails.
- Failed emits `SERVICE_FAILED` and is not `CONFIRMED`.
