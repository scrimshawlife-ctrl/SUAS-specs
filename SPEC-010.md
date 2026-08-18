# SPEC-010.md — Owner review worksheet: scaling contract

**Status:** `BLOCKED_BY_SPEC_006_SPEC_007_SPEC_009`  
**Stack version:** `0.1.0` / `draft`  
**Owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-010  
**Required:** [SCALING.md](SCALING.md), [ARCHITECTURE.md](ARCHITECTURE.md), [DATA_MODEL.md](DATA_MODEL.md), [APIS.md](APIS.md)

Preflight only. No lifecycle advancement.

---

## 1. Objective

Accept measurable horizontal-scaling requirements without inventing adoption/capacity numbers or requiring premature distributed-system machinery.

---

## 2. Owner checklist

### Capacity epistemics
- [ ] Controlled pilot remains the only currently specified numeric operating population.
- [ ] D-021 owns the release-specific workload/capacity envelope.
- [ ] D-023 owns release-specific performance SLOs/alerts.
- [ ] No regional/multi-region registered-user forecast is treated as fact.

### Statelessness
- [ ] Correctness-critical session/workflow/idempotency/provider state is shared/persistent, not process-local.
- [ ] Any healthy app instance can serve any authorized request.
- [ ] Adding/removing app instances does not change domain semantics.

### Durable work
- [ ] Production-critical jobs survive restart.
- [ ] Duplicate job delivery is safe.
- [ ] Failed work is visible/replayable.
- [ ] Priority/admission controls prevent maintenance work from starving urgent support work.

### Concurrency
- [ ] Case creation/claim/assignment have deterministic one-winner semantics where exclusive.
- [ ] FulfillmentAttempt external mutations are idempotent.
- [ ] Auth challenge consumption is atomic.
- [ ] Settlement resolve retry cannot create duplicate cycles.
- [ ] Follow-Up stale jobs cannot mutate newer schedules.

### API / PostgreSQL
- [ ] Growing lists are bounded/cursor-paginated.
- [ ] Normal screens do not require full history scans.
- [ ] Connection pooling is required.
- [ ] High-volume/current projection/queue/idempotency access paths are indexable.
- [ ] Read replicas/partitioning/sharding remain evidence-driven.

### Tenant fairness / backpressure
- [ ] One tenant cannot trivially starve all others.
- [ ] Bursts create durable queued work instead of synchronous provider cascades.
- [ ] Provider rate limits/backpressure protect DB and external systems.
- [ ] Non-critical work may degrade before correctness-critical support work is lost/corrupted.

### Observability / load evidence
- [ ] Route, DB, queue, worker, claim-conflict, notification, provider, webhook, signal/event, Settlement conflict, and tenant-load metrics are observable.
- [ ] Load testing uses steady, burst, degraded dependency, and concurrency-correctness profiles.
- [ ] Exact target rates/concurrency/latency are recorded in release artifacts, not guessed in this spec.

---

## 3. Non-goals

No unsupported capacity forecast, Kubernetes/microservices/broker/cache mandate, speculative sharding, or multi-region active-active requirement.

---

## 4. Exit criteria

SPEC-010 may be accepted after dependencies and after the owner confirms the scale contract can be tested against a later D-021/D-023 release target without domain redesign.

**Implementation handoff:** none until SPEC-016 release.
