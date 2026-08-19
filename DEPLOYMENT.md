# DEPLOYMENT.md — Environments and production topology (SUAS v0.1.3)

**Lifecycle:** `released` via [RELEASE_MANIFEST-0.1.3.md](RELEASE_MANIFEST-0.1.3.md)
**Related:** [ENVIRONMENT.md](ENVIRONMENT.md), [ARCHITECTURE.md](ARCHITECTURE.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SECURITY.md](SECURITY.md), [OPERATIONS.md](OPERATIONS.md), [DECISIONS.md](DECISIONS.md)

Cloud/platform/database/job providers remain deferred for production. This file defines topology and environment separation; [ENVIRONMENT.md](ENVIRONMENT.md) defines runtime configuration, startup validation, external-effect safety, and build provenance.

## 1. Environments

| Environment | Purpose | Real veteran data | Real external support effects |
|---|---|---|---|
| `LOCAL` | developer work | forbidden | forbidden |
| `TEST` | automated tests / CI | forbidden | forbidden |
| `STAGING` | integrated synthetic validation/load/failure drills | forbidden | forbidden except non-real sandbox effects explicitly allow-listed |
| `PRODUCTION` | eventual live operation | blocked until SPEC-018 | blocked until SPEC-018 and relevant decisions close |

Each environment has separate databases, secrets, provider configuration, telemetry boundaries, durable work state, and user data. Environment class must be explicit and validated as required by ENVIRONMENT.md.

## 2. Production topology requirements

When production is eventually authorized, topology must support:

- one or more stateless SUAS application instances;
- PostgreSQL as logical system of record;
- durable production-critical async work surviving worker restart;
- independently scalable workers;
- provider adapter configuration by environment/tenant/coverage;
- authenticated provider/notification webhook ingress;
- environment-scoped secret storage;
- app/DB/job/notification/provider health telemetry;
- backup/restore process;
- build provenance identifying application commit/version, released spec/manifest, schema version, and environment.

Adding app/worker capacity must not require domain changes.

## 3. Rules

1. LOCAL/TEST/STAGING never use production veteran data.
2. Non-production communications/providers use disabled/fake/sink/manual/sandbox modes that cannot affect real veterans or scarce support resources.
3. STAGING supports provider fakes/sandboxes, visual tests, representative load profiles, and resilience drills.
4. Schema migrations run per environment and cite released specs.
5. Production-critical work never relies on volatile in-process-only queues.
6. Secrets never live in Git, domain rows, release manifests, logs, screenshots, or client bundles.
7. Deployment/configuration cannot redefine released semantics or enable manifest-UNAVAILABLE features.
8. Horizontal app/worker scaling must not require a domain/data rewrite.
9. Real provider adapters may be enabled only by a later released provider decision plus conformance/security/consent readiness.
10. Cache is an optimization, not accidental source of truth.
11. Invalid environment/feature/config combinations fail closed at startup.
12. A build must reject a DB/schema state it cannot safely operate against.

## 4. Current deferred production decisions

D-001 hosting, D-002 auth implementation, D-003 SMS, D-004 email, D-005 database hosting, D-007 retention, D-019–D-020 real service adapters; real Uber transportation effects under D-017; real Amadeus shelter inventory effects, holds, reservations, and cancellations under D-018; D-021 workload envelope, D-022 durable-job implementation, D-023 SLOs/alerts, and D-024 RTO/RPO remain production-unavailable under the current release boundary unless superseded by later readiness/decision evidence. Shelter reservation additionally remains `BLOCKED_BY_PAYMENT_ARCHITECTURE` absent a documented card-free enterprise contract.

## 5. Deployment verification before SPEC-018

Evidence must show:

- environment/secrets/data separation;
- startup configuration fail-closed behavior;
- at least two app instances can serve the same staging workload without correctness drift;
- worker restart does not lose acknowledged critical work;
- deploy/restart graceful behavior;
- webhook authentication/duplicate safety;
- provider-degraded drill;
- queue depth/age and provider health observability;
- restore exercise with duplicate-external-effect protection;
- schema compatibility/migration evidence;
- build provenance matches the released spec manifest;
- scale/resilience evidence maps to TESTING.md.

## 6. Non-goals

No Kubernetes/microservices mandate, speculative multi-region active-active, vendor selection by architecture, shared STAGING/PRODUCTION data or secrets, or requirement that providers expose APIs.
