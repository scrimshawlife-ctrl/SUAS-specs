# SPEC-006.md — Owner review worksheet: domain, data, event, architecture reconciliation

**Status:** `BLOCKED_BY_SPEC_001_THROUGH_SPEC_005`  
**Stack version:** `0.1.0` / `draft`  
**Owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-006  
**Required:** [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [EVENT_MODEL.md](EVENT_MODEL.md), [ARCHITECTURE.md](ARCHITECTURE.md)

This worksheet is preflight only. It must not be accepted before SPEC-001 through SPEC-005 are accepted. Agents do not tick owner checkboxes or change lifecycle.

---

## 1. Objective

Accept one coherent logical model for the already-specified product behavior without choosing physical migration syntax, cloud/queue vendors, or premature distributed-system machinery.

SPEC-006 must prove that earlier product contracts can be represented without hidden coupling or implementation invention.

---

## 2. Owner checklist

### Domain ownership

- [ ] Each canonical entity has exactly one logical write owner.
- [ ] Cases own Case lifecycle/assignment but do not own Settlement history.
- [ ] Settlement is a first-class module/entity, not a blob hidden on SupportCase.
- [ ] Command Idempotency has a persistent logical owner separate from Domain Events.
- [ ] Provider Router owns adapter/routing configuration but not Service Request state.

### Support Signal representation

- [ ] Primary signal computation has stable identity across duplicate/replayed jobs.
- [ ] Explicit-need signals have stable source identity when no Check-In exists.
- [ ] Overrides create new historical rows and do not mutate primary rows.
- [ ] Effective/current Support Signal projection is deterministic and does not depend on insertion order.
- [ ] Current projection remains efficiently queryable without deleting history.

### Case / assignment concurrency

- [ ] MVP one-active-case semantics can produce one deterministic winner under concurrent creation.
- [ ] Exclusive Case claim/assignment has at most one active assignment where required.
- [ ] Reassignment preserves prior assignment history.
- [ ] Stale UI/queue state cannot override mutation-time concurrency rules.

### Service Request / provider history

- [ ] Provider/FulfillmentAttempt state is distinct from canonical Service Request state.
- [ ] FulfillmentAttempt has stable external idempotency identity.
- [ ] Deliberate reroute creates a new attempt rather than mutating prior attempt identity.
- [ ] `PROVIDER_UNKNOWN` is representable and reconciled before duplicate-risk retry.
- [ ] Current assignment/provider presentation is deterministic from durable history.

### Follow-Up

- [ ] Follow-Up stores a schedule/version identity sufficient to reject stale due/overdue jobs.
- [ ] Business coordination-attempt count is distinct from notification delivery attempts and queue redelivery.
- [ ] `BLOCKING` vs `CARRIED_FORWARD` resolution disposition is representable.

### Settlement

- [ ] One Support Case may have many Settlements across reopen/resolution cycles.
- [ ] `resolution_cycle` or equivalent provides deterministic case-local history.
- [ ] Prior Settlement is preserved after reopen.
- [ ] New resolution creates a new Settlement rather than overwriting the previous cycle.
- [ ] Current/latest Settlement projection is deterministic and history-preserving.
- [ ] Settlement used to enter `RESOLVED` cannot be silently rewritten.

### Events / idempotency

- [ ] `event_id` is event identity, not a substitute for command/job idempotency.
- [ ] `idempotency_key`, correlation, causation, and request identifiers have distinct semantics.
- [ ] Required Domain Event publication cannot be permanently lost after domain commit.
- [ ] Outbox/equivalent is allowed as an implementation mechanism without becoming a business entity.
- [ ] Unsafe commands can persist/replay authoritative results across restart and horizontal instances.
- [ ] Reusing one idempotency key with conflicting request content fails rather than causing a second effect.

### Scale / tenancy

- [ ] Tenant scope is present on tenant-owned entities.
- [ ] High-volume/current projections have bounded/indexable access paths.
- [ ] No normal UI path requires unbounded history scans.
- [ ] Physical sharding/read replicas/partitioning remain evidence-driven later choices.

---

## 3. Accepted logical entities added/reconciled in preflight

The owner should explicitly confirm these logical concepts before SPEC-006 acceptance:

- `Settlement`
- `CommandIdempotencyRecord`
- `ProviderAdapterConfiguration`
- `FulfillmentAttempt`
- deterministic effective Support Signal projection
- deterministic current/latest Settlement projection
- Follow-Up `schedule_version`/equivalent stale-job identity
- Follow-Up `BLOCKING|CARRIED_FORWARD` resolution disposition

These are logical contracts. Exact SQL DDL/index syntax remains implementation work after release.

---

## 4. Physical representation freedom

Implementation may choose equivalent PostgreSQL mechanisms such as:

- partial unique indexes;
- exclusion/unique constraints;
- row/version compare-and-set;
- transaction locks;
- current-pointer columns vs deterministic indexed queries;
- transactional outbox;
- bounded persisted idempotency response vs result reference.

The choice is valid only if it proves the accepted logical invariants and scale/resilience tests. SPEC-006 does not require a particular ORM or database hosting vendor.

---

## 5. Non-goals

- application code;
- physical migrations;
- selecting PostgreSQL hosting;
- selecting queue/cache products;
- microservice extraction;
- closing D-001–D-024;
- accepting SPEC-007 or later.

---

## 6. Exit criteria

SPEC-006 may be owner-accepted only after:

1. SPEC-001 through SPEC-005 are accepted;
2. all checklist items above are reviewed;
3. [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [EVENT_MODEL.md](EVENT_MODEL.md), and [ARCHITECTURE.md](ARCHITECTURE.md) contain no contradiction in entity ownership, history, current projection, idempotency, tenancy, or concurrency semantics;
4. unresolved technology/provider choices remain explicit decisions rather than hidden implementation assumptions.

**Implementation handoff:** none until a released cut at SPEC-016.
