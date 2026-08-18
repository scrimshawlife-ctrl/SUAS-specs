# SPEC-010.md — Owner acceptance record: scaling contract

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the measurable horizontal-scaling contract.

Accepted requirements:

- Pilot population is operating scope, not a technical ceiling.
- No unsupported regional/multi-region user forecast is canonical.
- Correctness-critical state is shared/persistent; any healthy app instance can serve authorized requests.
- Production-critical jobs are durable by contract, duplicate-safe, observable, and replayable.
- Case/assignment/auth/Settlement/Follow-Up/provider concurrency rules preserve one logical effect under contention/replay.
- Growing lists are bounded/paginated; normal screens do not require full history scans.
- PostgreSQL connection pooling and indexable high-volume/current projections are required; replicas/partitioning/sharding are evidence-driven later choices.
- Tenant fairness and backpressure prevent one tenant/burst/provider from trivially starving or corrupting the system.
- Load evidence must cover steady, burst, degraded-dependency, horizontal-instance, and concurrency-correctness profiles.

D-021 and D-023 production workload/SLO targets remain deferred for v0.1.0 because production deployment is unavailable. No Kubernetes, microservice, broker, cache, sharding, or multi-region mandate is introduced.
