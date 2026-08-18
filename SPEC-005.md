# SPEC-005.md — Resource / referral / fulfillment / follow-up / settlement owner review worksheet (SUAS v0.1)

**Status:** `BLOCKED_BY_SPEC_004`  
**Lifecycle:** `draft` / `0.1.0`  
**Dependencies:** SPEC-004 accepted  
**Specification owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-005  
**Required artifacts:** [RESOURCES.md](RESOURCES.md), [REFERRALS.md](REFERRALS.md), [FULFILLMENT.md](FULFILLMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [SETTLEMENT.md](SETTLEMENT.md)

This worksheet is preflighted specification work only. It cannot be accepted before SPEC-004. Agents must not tick owner boxes or promote lifecycle.

---

## 1. Objective

Accept the completion half of the SUAS coordination loop:

```text
Resource / Referral / Fulfillment Attempt
            ↓
       Fulfillment
            ↓
        Follow-Up
            ↓
        Settlement
```

The goal is to make service coordination completable without confusing catalog availability, Referral delivery, provider activity, Fulfillment, Follow-Up, or Settlement.

---

## 2. Governing invariants

1. Resource freshness is not live provider availability.
2. ServiceProvider/Resource validity does not require an API.
3. Provider outage does not erase/deactivate a valid manual/referral resource automatically.
4. Referral send is not Fulfillment and not proof of service receipt.
5. Referral delivery retries are not new Referrals or Follow-Up retries.
6. Fulfillment Attempt is provider/manual execution evidence, not Service Request/Fulfillment state.
7. Provider completion cannot bypass SUAS confirmation.
8. Failed Fulfillment Attempt does not automatically make a Service Request unfulfillable.
9. Follow-Up is first-class; notification/job retries are not Follow-Up business retries.
10. Blocking vs carried-forward Follow-Up must become explicit/queryable before release.
11. Settlement is a durable resolution-cycle record, not a mutable single summary overwritten on reopen.
12. Case reopen preserves prior Settlement and next resolution creates a new Settlement.
13. Resolve/Settlement creation is atomic/idempotent/stale-state protected.
14. Funding/billing remains `FUTURE` and separate from Fulfillment/Settlement.

---

## 3. Owner checklist

### RESOURCES.md

- [ ] Resource catalog fields and MVP categories are correct.
- [ ] API/manual/referral/information-only providers are all valid.
- [ ] Freshness bands are accepted as operational recommendations, not legal availability claims.
- [ ] Catalog freshness is explicitly distinct from live Provider Offer/availability.
- [ ] Provider outage does not silently deactivate an otherwise valid manual Resource.
- [ ] Resource verification is explicit, audited, and idempotent.
- [ ] Veteran-visible Resource fields exclude adapter/configuration/secrets/internal routing data.
- [ ] Resource search/list is bounded/paginated and tenant scoped.

### REFERRALS.md

- [ ] Referral remains distinct from Service Request/Fulfillment.
- [ ] Send requires use-time consent/basis and minimum-necessary projection.
- [ ] Duplicate send command does not duplicate logical Referral/disclosure.
- [ ] Email/SMS/in-app delivery retry does not create a new Referral transition.
- [ ] Delivery receipt cannot masquerade as destination acceptance/service completion unless the accepted protocol proves that meaning.
- [ ] Referral `COMPLETED` does not auto-create ServiceFulfillment.
- [ ] Linked Follow-Up remains independent until explicitly completed/cancelled.

### FULFILLMENT.md

- [ ] Assignment is not Fulfillment.
- [ ] Fulfillment states `ACCEPTED`, `STARTED`, `COMPLETED`, `CONFIRMED` plus documented exceptions are correct.
- [ ] Fulfillment Attempt identities/statuses are separate from canonical Fulfillment/Request states.
- [ ] Same-attempt external retries reuse stable idempotency identity.
- [ ] Deliberate provider switch creates a new attempt.
- [ ] `PROVIDER_UNKNOWN` reconciles before duplicate-risk mutation.
- [ ] Provider completion is evidence, not auto-confirmation.
- [ ] Failed/cancelled attempt may lead to another attempt without creating a new Service Request.
- [ ] Manual Adapter paths remain valid for all MVP capabilities.
- [ ] Funding/payment-card workflow remains out of MVP.

### FOLLOWUP.md

- [ ] Follow-Up remains distinct from Case Note/Notification/Settlement.
- [ ] `retry_count` is coordination-attempt retry count only.
- [ ] Notification provider retries and queue redelivery do not increment Follow-Up business retries.
- [ ] Due/overdue jobs are durable, idempotent, and stale-schedule protected.
- [ ] Reschedule invalidates old due-work identity/version.
- [ ] Case close does not auto-complete Follow-Ups.
- [ ] Blocking vs carried-forward Follow-Up distinction is accepted as a required SPEC-006 representation.

### SETTLEMENT.md

- [ ] Settlement is one durable resolution-cycle record, not clinical outcome or Fulfillment.
- [ ] Required Settlement content is sufficient and reference-oriented rather than duplicating sensitive raw payloads.
- [ ] Resolve + Settlement commit is atomic/idempotent/stale-state protected.
- [ ] Case reopen preserves prior Settlement and later resolution creates a new Settlement.
- [ ] Latest/effective Settlement cannot be inferred from insertion order alone.
- [ ] Open Follow-Ups are explicitly classified blocking vs carried-forward.
- [ ] Veteran-visible Settlement does not expose internal-only referenced Case/Provider data.
- [ ] Funding/billing remains future and not required for settlement.

---

## 4. Required SPEC-006 schema reconciliation

SPEC-005 intentionally defines behavior before final logical/physical representation.

SPEC-006 must resolve at least:

1. a first-class `Settlement` entity/table or equivalent durable structure;
2. one-to-many Case → Settlement resolution-cycle history;
3. deterministic current/latest Settlement pointer/projection without losing prior rows;
4. explicit blocking/carried-forward Follow-Up field/rule rather than free-text inference;
5. stable Follow-Up schedule/version identity for stale-job invalidation;
6. Referral send/idempotency representation if API-level idempotency alone is insufficient for audit/recovery;
7. Resource/Provider Offer boundary and any persisted live-availability cache semantics;
8. current ServiceFulfillment/history model if more than one fulfillment record per Request becomes necessary;
9. unique/idempotent external FulfillmentAttempt constraints;
10. event/outbox linkage needed for replay-safe completion/resolution effects.

These are required handoffs, not permission for implementation to choose silently.

---

## 5. Completion semantics

The following are deliberately non-equivalent:

```text
Resource found
≠ Referral sent
≠ Provider assigned
≠ Provider accepted
≠ Service started
≠ Service completed
≠ Veteran/responder confirmed
≠ Follow-Up completed
≠ Case settled
```

Production analytics/UI must preserve these distinctions.

---

## 6. Test evidence required later

- stale Resource warning/freshness boundary tests;
- provider outage/manual Resource fallback test;
- duplicate Resource verification idempotency test;
- duplicate Referral send test;
- ambiguous referral transport/delivery test;
- Fulfillment Attempt duplicate external-effect test;
- provider unknown-outcome reconciliation test;
- manual fulfillment path test for all MVP categories;
- stale/duplicate Follow-Up due job tests;
- notification retry vs Follow-Up retry separation test;
- blocking vs carried-forward Follow-Up resolution tests;
- duplicate resolve creates one Settlement/`CASE_RESOLVED` test;
- reopen → second Settlement history test;
- veteran Settlement visibility test.

---

## 7. Exit criteria

SPEC-005 may become owner-accepted only after SPEC-004 is accepted and the owner confirms the checklist.

Exit means:

- Resource/Referral/Fulfillment/Follow-Up/Settlement concepts and transitions are accepted;
- provider/manual fulfillment semantics are coherent;
- retry/idempotency meanings are separated;
- repeated case resolution history is preserved by contract;
- required schema questions are explicitly handed to SPEC-006;
- funding remains outside MVP.

**Implementation authority:** none until SPEC-016 release.

---

## 8. Non-goals

- selecting provider vendors;
- accepting SPEC-006 schema prematurely;
- adding billing/payment workflows;
- equating referrals/provider callbacks with Fulfillment;
- hiding unresolved schema handoffs in free text;
- clinical outcome measurement;
- agents self-accepting.
