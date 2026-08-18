# SPEC-011.md — Owner review worksheet: resilience and degradation

**Status:** `BLOCKED_BY_SPEC_007_SPEC_009_SPEC_010`  
**Stack version:** `0.1.0` / `draft`  
**Owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-011  
**Required:** [RESILIENCE.md](RESILIENCE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [AUTH.md](AUTH.md), [FOLLOWUP.md](FOLLOWUP.md), [EVENT_MODEL.md](EVENT_MODEL.md), [OPERATIONS.md](OPERATIONS.md)

Preflight only. No lifecycle advancement.

---

## 1. Objective

Accept failure/recovery semantics that preserve workflow correctness, consent, history, and operator visibility under duplicate, delayed, stale, restarted, degraded, or ambiguous execution.

---

## 2. Owner checklist

- [ ] App/worker restart cannot lose production-critical acknowledged work.
- [ ] Duplicate API/job/webhook delivery cannot duplicate logical business effects.
- [ ] Command replay after lost response returns/reuses original authoritative result.
- [ ] Event/outbox recovery prevents permanent loss of required Domain Events after commit.
- [ ] Follow-Up stale jobs cannot mutate newer schedule state.
- [ ] Session/user/membership revoke remains authoritative across app instances/restarts.
- [ ] Provider unknown outcomes reconcile before duplicate-risk retry.
- [ ] Notification/provider outage preserves parent workflow correctness.
- [ ] Manual fallback is available where policy permits.
- [ ] Backpressure/tenant fairness prevents low-priority work from starving urgent support work.
- [ ] Failed/dead-letter/quarantined work remains inspectable and replay is audited.
- [ ] Restore preserves idempotency/Settlement/provider-attempt history required to avoid duplicate side effects.
- [ ] D-024 owns release-specific RTO/RPO; no unsupported recovery number is canonical here.

---

## 3. Required staging failure drills

Owner should confirm the minimum drill set in RESILIENCE includes provider/notification outages, duplicate/out-of-order callbacks, worker restart, queue backlog, DB commit uncertainty, duplicate API retry, concurrent Settlement resolve, stale Follow-Up due job, cross-instance session revoke, event publisher crash, and restore with pending provider attempts.

---

## 4. Non-goals

No zero-downtime guarantee, infinite retries, unsupported RTO/RPO promise, or multi-region active-active requirement.

---

## 5. Exit criteria

SPEC-011 may be accepted after dependencies and after the owner confirms failure behavior is explicit, replay-safe, testable, and provider-neutral.

**Implementation handoff:** none until SPEC-016 release.
