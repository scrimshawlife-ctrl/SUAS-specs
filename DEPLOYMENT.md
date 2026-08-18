# DEPLOYMENT.md — Environments and production topology (SUAS v0.1)

**Related:** [ARCHITECTURE.md](ARCHITECTURE.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SECURITY.md](SECURITY.md), [OPERATIONS.md](OPERATIONS.md), [DECISIONS.md](DECISIONS.md), [FRICTION.md](FRICTION.md)

**Status:** `draft` / `0.1.0`. Cloud/platform/database/job providers remain `DECISION_PENDING`.

---

## 1. Purpose

Define environment separation and minimum production topology without locking a cloud, database host, durable-job product, cache, or external service provider.

---

## 2. Environments

| Environment | Purpose | Production veteran data? |
|---|---|---|
| `LOCAL` | Developer work | No |
| `TEST` | Automated tests / CI | No; fixtures only |
| `STAGING` | Pre-production integration/load/failure drills | No; synthetic data only |
| `PRODUCTION` | Real pilot/production operation | Yes |

Each environment has separate databases, secrets, provider credentials/configuration, telemetry boundaries, queues/jobs, and user data.

---

## 3. Production topology requirements

Production must support:

- one or more stateless SUAS application instances;
- PostgreSQL as logical system of record;
- durable production-critical async work that survives worker restart;
- independently scalable worker capacity;
- provider adapter configuration per environment/tenant/coverage;
- authenticated provider/notification webhook ingress;
- environment-scoped secrets; provider credentials are not domain data;
- health/metrics for app, DB, jobs, notification channels, and fulfillment adapters;
- backup/restore process.

The first deployment may run minimal instance counts, but deployment topology must not require domain changes to add app/worker capacity.

---

## 4. Rules

1. No production data in LOCAL, TEST, or STAGING.
2. Non-production communications/providers must use sinks, fakes, sandboxes, or allow-listed test destinations; never message/book real veteran support by accident.
3. STAGING must support representative provider fakes/sandboxes, load profiles, and resilience drills.
4. Schema migrations run per environment; production contract changes cite released specs.
5. Production-critical work may not rely on a volatile in-process-only queue.
6. Secrets do not live in Git, Resource rows, ServiceProvider rows, or ProviderAdapterConfiguration rows.
7. Deploy does not redefine specification.
8. Horizontal app/worker scaling must not require a domain/data-model rewrite.
9. Production provider adapters are enabled only when configuration, consent projection, webhook/auth, and conformance tests are ready.
10. Cache, if added, is an optimization and must not become accidental source of truth.

---

## 5. Unsettled deployment decisions

| Topic | Status |
|---|---|
| Cloud/application hosting | D-001 `DECISION_PENDING` |
| Database hosting | D-005 `DECISION_PENDING` |
| Auth/SMS/email providers | D-002–D-004 |
| Transportation/room/food/peer provider adapters | D-017–D-020 |
| First-release scale target | D-021 |
| Durable job/queue product | D-022 |
| Performance SLO/alert thresholds | D-023 |
| RTO/RPO and backup objectives | D-024 |
| Retention/backup duration details | D-007 adjacent |

---

## 6. Deployment verification

Before production readiness:

- environment secrets/data separation verified;
- at least two app instances can serve the same staging workload without correctness drift;
- worker restart does not lose acknowledged production-critical test work;
- deployment/restart graceful behavior tested;
- provider webhook endpoints reject unauthenticated callbacks;
- one provider outage/degraded drill completed in staging;
- queue depth/age and provider health are observable;
- restore procedure executed and recorded for the release environment;
- scale/resilience evidence maps to [TESTING.md](TESTING.md).

---

## 7. Non-goals

- Multi-region active-active as an MVP requirement
- Kubernetes/microservices as requirements
- Naming a vendor as architecture
- Shared DB/secrets between STAGING and PRODUCTION
- Requiring API-backed providers for every service
