# SETTLEMENT.md — Explicit resolution record (SUAS v0.1)

**Status:** `draft` / `0.1.0` / SPEC-005 preflight; not implementation authority.  
**Related:** [CASES.md](CASES.md), [FULFILLMENT.md](FULFILLMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [PRODUCT.md](PRODUCT.md), [ARCHITECTURE.md](ARCHITECTURE.md), [ANALYTICS.md](ANALYTICS.md), [EVENT_MODEL.md](EVENT_MODEL.md)

**Actors:** Assigned Responder (authors), Veteran (may confirm own-visible fields), Org-admin (review), SUAS System Administrator (audit).

---

## 1. Purpose

A Settlement is an **explicit, durable resolution record for one resolution cycle of a Support Case**. It is not a clinical outcome and not a Fulfillment.

A Case cannot enter `RESOLVED` without a Settlement.

---

## 2. Required content

Each Settlement records at least:

| Item | Meaning |
|---|---|
| `support_case_id` | Case being resolved |
| resolution cycle/version identity | distinguishes repeated resolve/reopen cycles |
| what was requested | referenced Service Requests and statuses |
| what occurred | referenced Referrals, Contact Attempts, assignments/actions |
| what was fulfilled | referenced ServiceFulfillments, including partial/failed/cancelled outcomes |
| what remains unresolved | explicit unmet/open needs |
| who confirmed | responder required; veteran optional where applicable |
| `settled_at` | server-authoritative time |
| remaining Follow-Up | explicit carried-forward responsibilities, owners, due dates |
| author/actor | accountable human author |

Settlement should primarily reference canonical records rather than duplicate whole notes/provider payloads into settlement text.

---

## 3. Resolution-cycle history

A Support Case may be reopened under [CASES.md](CASES.md). Therefore Settlement history must support **more than one Settlement per Case over time**.

Rules:

1. A prior Settlement is never overwritten when a Case reopens.
2. Reopen starts a new resolution cycle.
3. Resolving that reopened cycle creates a new Settlement.
4. Each `CASE_RESOLVED` fact identifies the Settlement for that resolution cycle.
5. `support_case_id → one mutable settlement_id` is not sufficient as the sole history model unless it is explicitly a pointer to the current/latest Settlement **and** prior Settlement rows remain independently addressable.
6. Effective/latest Settlement projection must be deterministic and must not rely on insertion order alone.
7. Case close/reopen history remains auditable.

The exact schema (Settlement table + cycle number/current pointer/link table or equivalent) is a **required SPEC-006 reconciliation item**. Implementation must not flatten repeated resolution history into one overwritten record.

---

## 4. Remaining Follow-Up semantics

Settlement must distinguish:

- **blocking Follow-Up** that must be completed/cancelled before resolution; and
- **carried-forward Follow-Up** that may remain after resolution because the Settlement explicitly records owner, due date, and reason it remains open.

This distinction must become a first-class, queryable rule under SPEC-006; it must not be inferred from note/free-text wording.

A Case cannot resolve with an unclassified open Follow-Up.

---

## 5. Validation and idempotency

Resolve + Settlement creation is a contested/retryable command path.

Required semantics:

1. Settlement validation and Case `RESOLVED` transition occur atomically/equivalently.
2. Resolve without required Settlement content fails.
3. Replaying the same logical resolve command does not create a duplicate Settlement or `CASE_RESOLVED` event.
4. A stale resolve command cannot resolve a Case whose blocking work changed after the command's expected state/version.
5. A later deliberate re-resolution after reopen uses a new resolution-cycle identity and creates a new Settlement.
6. Generative models may assist only under later accepted assistive rules; they cannot autonomously author/commit Settlement or decide closure.

---

## 6. Veteran visibility

Veteran may read own Settlement fields intended for them under MVP visibility rules.

Settlement is not a clinical chart. Full Case Notes, internal responder Contact Attempts, provider raw payloads, and other internal-only fields are not exposed merely because a Settlement references those records.

---

## 7. Funding / billing boundary

Status: `FUTURE`.

```text
Fulfillment → Funding Eligibility → Funding Source → Optional Billing Adapter
```

Do not assert Medi-Cal billability. A Service Request may be fulfilled and settled with no funding source.

Provider estimated cost is informational unless a later accepted funding spec authorizes more.

---

## 8. Events/audit

- `CASE_RESOLVED` identifies the Case + Settlement/resolution cycle.
- Settlement create/amend/review/reopen linkage is audited.
- Historical Settlement rows are not mutated to represent a later resolution cycle.

If Settlement itself later needs dedicated Domain Event names, they require explicit [EVENT_MODEL.md](EVENT_MODEL.md) reconciliation.

---

## 9. Non-goals

- clinical outcome coding;
- suicides-prevented narrative;
- Medi-Cal claim generation;
- Settlement as substitute for missing Fulfillment records;
- overwriting prior Settlement on Case reopen;
- deriving open Follow-Up blocking behavior from free text;
- creating duplicate Settlement rows from command retries.

---

## 10. Testability

- resolve without Settlement fails;
- missing required Settlement content fails;
- blocking open Follow-Up prevents resolution;
- carried-forward Follow-Up requires explicit classification/owner/due date;
- duplicate resolve command creates one logical Settlement/`CASE_RESOLVED`;
- stale resolve conflicts if blocking state changed;
- reopen preserves prior Settlement and next resolve creates a new Settlement;
- latest/effective Settlement selection is deterministic once SPEC-006 defines the projection;
- no MVP billing fields required;
- veteran-visible Settlement excludes internal-only referenced data.
