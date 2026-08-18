# SPEC-004.md — Case / Service Request / responder workflow owner review worksheet (SUAS v0.1)

**Status:** `BLOCKED_BY_SPEC_001_AND_SPEC_002`  
**Lifecycle:** `draft` / `0.1.0`  
**Dependencies:** SPEC-001 accepted; SPEC-002 accepted  
**Specification owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-004  
**Required artifacts:** [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md)

This worksheet is preflighted specification work only. It cannot be accepted before its dependencies. Agents must not tick owner boxes or promote lifecycle.

---

## 1. Objective

Accept the canonical Support Case and Service Request state machines plus responder/QRF operating actions, including the concurrency/idempotency rules required for production scale.

SPEC-004 must preserve the referenced MVP's fast responder/QRF experience while ensuring that queue freshness, retries, concurrent responders, provider callbacks, and duplicate jobs cannot corrupt canonical state.

---

## 2. Governing invariants

1. Support Case and Service Request remain distinct.
2. Assignment is not Fulfillment.
3. Referral is not Service Request or Fulfillment.
4. Provider integration status is not Service Request status.
5. Case/Request transitions are command-driven and explicit; no hidden status mutation.
6. Contested state changes use mutation-time stale-state checks and atomic one-winner semantics.
7. Duplicate command/job delivery must not duplicate logical business effects/events.
8. Queue/browser state is advisory; server mutation checks are authoritative.
9. Provider webhooks/callbacks are evidence inputs and cannot bypass authorization, consent, transition, fulfillment, or confirmation rules.
10. Responder console remains action-first, mobile-usable, and non-EHR in character.
11. Cross-tenant queue/action access is forbidden.
12. Generative AI does not claim/assign/resolve/close cases or perform service qualification.

---

## 3. Owner checklist

### CASES.md

- [ ] Canonical Case states remain exactly `OPEN`, `TRIAGED`, `ASSIGNED`, `ACTIVE`, `FOLLOWUP`, `RESOLVED`, `CLOSED`.
- [ ] MVP one-non-closed-case-per-veteran remains explicitly `INFERRED` / future multi-case policy is not silently implemented.
- [ ] Concurrent/replayed Case creation cannot create duplicate active Cases under the MVP default.
- [ ] `CLAIM_CASE` and exclusive assignment have atomic one-winner semantics.
- [ ] Reassignment atomically releases prior assignment and creates successor ownership.
- [ ] Claim/assign command replay is idempotent.
- [ ] Escalation edges are explicit and do not permit impossible unassigned `OPEN`/`TRIAGED → ACTIVE` transitions.
- [ ] Resolve requires Settlement and blocking work rules.
- [ ] Close/reopen retains history and does not erase prior Settlement.
- [ ] Queue access is bounded/paginated and mutation-time state is authoritative.

### DISPATCH.md

- [ ] Canonical Service Request happy-path and exception states are correct.
- [ ] Provider/FulfillmentAttempt integration statuses remain separate from canonical Request states.
- [ ] Competing incompatible transitions yield one valid winner and conflicts for losers.
- [ ] Assignment from `MATCHING` cannot silently produce two canonical current assignments.
- [ ] Provider callback cannot jump directly to canonical fulfillment/confirmation without documented command prerequisites.
- [ ] `PROVIDER_COMPLETED` is evidence, not auto-confirmation.
- [ ] `PROVIDER_UNKNOWN` requires reconciliation, not blind retry or terminal-state inference.
- [ ] Expiry jobs are stale-state protected and idempotent.
- [ ] Reroute/rematch preserves prior attempt history and creates deliberate new attempt identity where required.
- [ ] Provider disclosure is re-evaluated/minimized when routing changes.

### RESPONDER_WORKFLOWS.md

- [ ] Named responder actions are complete and no unnamed privileged writes are implied.
- [ ] `CLAIM_CASE` UX preserves fast QRF response while waiting for committed server ownership.
- [ ] Queue read may be stale; claim conflict refresh path is explicit.
- [ ] Queue pagination/filtering preserves tenant isolation.
- [ ] Browser/client double-submit prevention is not relied on for correctness.
- [ ] Contact Attempt and Case Note remain distinct.
- [ ] Duplicate Contact Attempt command replay does not create duplicate logical event/fact.
- [ ] Escalation follows CASES/DISPATCH explicit edges.
- [ ] Responder surface remains coordination-oriented, not a clinical/EHR console.

### DOMAIN_MODEL.md handoff

- [ ] Draft domain entities can represent CaseAssignment, ServiceRequest, FulfillmentAttempt, ServiceFulfillment, provider configuration, and immutable events without aliasing.
- [ ] SPEC-004 acceptance does not accept SPEC-006 physical/logical data choices prematurely.
- [ ] Exact current assignment/provider/effective-signal projections that require schema decisions are explicitly handed forward rather than guessed.

---

## 4. Concurrency model

SPEC-004 requires compare-and-set/optimistic version/transaction/constraint-backed **semantics**, not a specific database mechanism.

For a contested command:

```text
read queue/current state
      ↓
user/job submits command
      ↓
server re-checks expected state + authz in atomic mutation
      ├─ valid winner → commit state + event
      └─ stale/loser → conflict, no partial effect
```

This model applies to at least:

- Case create under one-active-case policy;
- Case claim/assignment/reassignment;
- Service Request provider assignment;
- incompatible Request transition races;
- resolve/close races;
- expiry jobs competing with human transitions.

---

## 5. MVP responder/QRF boundary

Production safety must not make the responder experience slow or bureaucratic.

The accepted UI later under SPEC-008 should preserve:

- urgent/current need visibility;
- on-duty/availability state;
- one-tap/low-friction claim action;
- obvious ownership;
- clear conflict feedback when another responder wins;
- quick contact/escalate/resource/service actions;
- mobile-first low navigation depth.

Concurrency controls belong on the server and should surface as concise user feedback, not long locking workflows.

---

## 6. Event/data handoff

SPEC-004 relies on replay-safe event semantics from SPEC-003 but does not itself accept the later full data model.

Before SPEC-006 can be accepted, it must reconcile at least:

- unique/active Case ownership representation;
- one-active-case MVP constraint or equivalent invariant;
- Request assignment/provider linkage history;
- command idempotency storage/lookup;
- Settlement/reopen history linkage;
- event/outbox implementation semantics.

---

## 7. Test evidence required later

- concurrent Case creation one-winner test;
- concurrent `CLAIM_CASE` test;
- claim replay/idempotency test;
- atomic reassignment test;
- stale queue claim conflict test;
- competing Service Request assignment test;
- incompatible Request transition race test;
- stale/duplicate expiry job test;
- provider callback cannot bypass canonical transitions test;
- cross-tenant queue/action denial test;
- invalid escalation edge test;
- duplicate responder command event-deduplication test.

See [TESTING.md](TESTING.md).

---

## 8. Exit criteria

SPEC-004 may become owner-accepted only after dependencies are accepted and the owner confirms the checklist.

Exit means:

- Case and Service Request state names/transitions are frozen for the current stack;
- concurrency/idempotency semantics are accepted;
- responder/QRF actions are accepted;
- provider evidence cannot bypass canonical state;
- remaining schema representation questions are handed explicitly to SPEC-006;
- assignment remains distinct from fulfillment.

**Implementation authority:** none until SPEC-016 release.

---

## 9. Non-goals

- selecting provider vendors;
- accepting full data model/architecture prematurely;
- introducing microservices;
- adding hidden states;
- auto-claim/auto-resolve via model;
- clinical/EHR workflow expansion;
- agents self-accepting.
