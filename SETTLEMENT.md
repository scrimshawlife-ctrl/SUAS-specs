# SETTLEMENT.md — Explicit resolution record (SUAS v0.1)

**Related:** [CASES.md](CASES.md), [FULFILLMENT.md](FULFILLMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [PRODUCT.md](PRODUCT.md), [ARCHITECTURE.md](ARCHITECTURE.md), [ANALYTICS.md](ANALYTICS.md)

**Actors:** Assigned Responder (authors), Veteran (may confirm), Org-admin (review), SUAS System Administrator (audit).

---

## 1. Purpose

A Settlement is an **explicit resolution record** for a Support Case. It is **not** a clinical outcome and **not** a Fulfillment.

A case cannot enter `RESOLVED` without a Settlement ([CASES.md](CASES.md)).

---

## 2. Required content

The Settlement must record:

| Item | Meaning |
|---|---|
| What was requested | List of Service Requests (category, summary, status) |
| What occurred | Referrals sent, contacts made (by reference to events/notes), assignments |
| What was fulfilled | ServiceFulfillment outcomes, including `PARTIAL` / `FAILED` / `CANCELLED` |
| What remains unresolved | Requests not fulfilled; unmet need text |
| Who confirmed | Actor ids (responder required; veteran optional) |
| When | `settled_at` |
| Remaining Follow-Up | Open follow-ups must be completed, cancelled, or explicitly listed as remaining with owners and due dates |

---

## 3. Rules

- Settlement does not delete history.
- Settlement does not imply the veteran is "well" or that risk is gone.
- A later reopen of the case ([CASES.md](CASES.md)) leaves the prior Settlement in place and requires a new Settlement to resolve again.
- Generative models must not author a Settlement as an unattended write ([SAFETY.md](SAFETY.md)).

---

## 4. Funding / Medi-Cal / billing boundary

**Status:** `FUTURE`. Do not assert that SUAS activities are Medi-Cal billable.

Logical path (not implemented in MVP):

```
Fulfillment → Funding Eligibility → Funding Source → Optional Billing Adapter
```

Possible future funding sources (names only; no eligibility claimed):

- sponsor
- donation
- nonprofit
- county program
- grant
- reimbursable program

A Service Request may be fulfilled with no funding source. Funding is not a prerequisite for Settlement.

See [PRODUCT.md](PRODUCT.md) §10 and [ARCHITECTURE.md](ARCHITECTURE.md) §14.

---

## 5. Events

`CASE_RESOLVED` is emitted when the case transitions to `RESOLVED` with a Settlement attached. Settlement create/update is audited.

---

## 6. Non-goals

- Clinical outcome coding
- Suicides-prevented narrative
- Medi-Cal claim generation
- Using Settlement as a substitute for missing Fulfillment records

---

## 7. Testability

- Resolve without Settlement fails.
- Settlement without the required content fields fails validation.
- Remaining follow-ups must be listed or closed.
- No billing fields required in MVP.
