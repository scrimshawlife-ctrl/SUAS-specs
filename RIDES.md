# RIDES.md — Ride-adapter contract for transportation coordination

**Related:** [PRODUCT.md](PRODUCT.md), [DISPATCH.md](DISPATCH.md), [FULFILLMENT.md](FULFILLMENT.md), [SETTLEMENT.md](SETTLEMENT.md), [ISLANDS.md](ISLANDS.md), [DECISIONS.md](DECISIONS.md), [FENCE_POSTS.md](FENCE_POSTS.md)  
**Status:** `draft` / unreleased / not implementation authority

---

## 1. Purpose

Define the draft adapter contract for transportation booking during the weekend build direction.

This file is **additive** to the existing `TRANSPORTATION` Service Request contract. It does **not** replace the Service Request state machine in [DISPATCH.md](DISPATCH.md).

---

## 2. Adapter input contract

A ride-booking command requires these ride-operational fields:

| Field | Required | Meaning |
|---|---|---|
| `pickup` | yes | Pickup address or pickup instructions required to dispatch the ride. |
| `destination` | yes | Destination address or destination instructions required to dispatch the ride. |
| `rider_contact` | yes | Rider-reachable contact detail used for ride coordination. |
| `who_pays` | yes | Who is expected to pay or authorize payment. This is descriptive in draft status and does not settle funding policy. |

Provider payloads may include only ride-operational data needed to execute the booking.

Provider payloads must **not** include:

- reason for the ride
- Check-In answers
- Support Signal basis
- distress framing

See `G4` in [FENCE_POSTS.md](FENCE_POSTS.md).

---

## 3. Human dispatch required

Ride dispatch is a **human dispatch MUST** contract.

Rules:

1. A responder or dispatcher initiates or confirms the booking.
2. The adapter does not auto-book from a veteran Check-In, Support Signal, or front-door event.
3. The adapter does not auto-decide ride eligibility from funding or distress context.
4. The adapter does not replace crisis-resource surfacing or emergency guidance in [SAFETY.md](SAFETY.md).

---

## 4. Adapter output contract

The ride adapter returns:

- `ride_booking_id`
- a status stream with these required statuses:
  - `booked`
  - `picked_up`
  - `completed`

Adapter status is not the same as Service Request status.

Required mapping rule:

- The Service Request still follows [DISPATCH.md](DISPATCH.md).
- Fulfillment still follows [FULFILLMENT.md](FULFILLMENT.md).
- A ride adapter status must not silently mutate the Service Request into a different state outside documented transitions.

---

## 5. Supported stub adapter shapes

Rev 3 draft scope allows stub adapters for:

- Uber Guest
- Lyft Concierge
- voucher
- volunteer

These are stub capability names only. They do not assert partner availability, contracting status, or legal approval.

**Waymo is out of scope.** Do not use an unofficial API.

---

## 6. Funding and cost guardrails

Funding remains open in [DECISIONS.md](DECISIONS.md) `D-010`.

Rules:

1. `who_pays` and any funding-rail reference are fields only in this draft.
2. Cost guardrails fail to a human review path.
3. Cost guardrails never auto-deny a ride.
4. Cost guardrails never block the 988 / Veterans Crisis Line crisis path.

This file does not define billing, reimbursement, or Medi-Cal eligibility.

---

## 7. Non-goals

- Replacing the Service Request state machine
- Sending check-in, reason-for-travel, or distress details to ride providers
- Auto-dispatch from a Support Signal
- Claiming provider partnerships that are not specified
- Using unofficial Waymo APIs

---

## 8. Testability

| Check | Pass condition |
|---|---|
| Required ride fields | Booking requires pickup, destination, rider contact, and who-pays fields. |
| Provider minimization | Provider payload excludes reason, Check-In answers, Support Signal basis, and distress framing. |
| Human dispatch | Booking requires a documented human action. |
| Status boundary | Adapter status stream is `booked` → `picked_up` → `completed` and does not replace [DISPATCH.md](DISPATCH.md) states. |
| Cost fail-closed | Cost guardrails escalate to a human path and do not auto-deny or block crisis-resource access. |
