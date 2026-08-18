# SPEC-013.md — Owner review worksheet: deployment, operations, incident/recovery

**Status:** `BLOCKED_BY_SPEC_007_SPEC_010_SPEC_011_SPEC_012`  
**Stack version:** `0.1.0` / `draft`  
**Owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-013  
**Required:** [DEPLOYMENT.md](DEPLOYMENT.md), [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [SECURITY.md](SECURITY.md), [RESILIENCE.md](RESILIENCE.md)

Preflight only. No lifecycle advancement.

---

## 1. Objective

Accept environment, day-2 operation, incident, and recovery duties without selecting vendors or inventing staffing/SLO/legal timelines.

---

## 2. Owner checklist

### Deployment
- [ ] LOCAL/TEST/STAGING/PRODUCTION are isolated for DB, secrets, queues/jobs, provider config, telemetry, and user data.
- [ ] No production veteran data reaches non-production.
- [ ] Production supports stateless app instances + durable workers without domain rewrite.
- [ ] Provider/notification webhook ingress is authenticated.
- [ ] Provider secrets remain deployment secrets, not domain/client data.
- [ ] D-001/D-005/D-022 remain vendor/product decisions rather than architecture facts.

### Operations
- [ ] Veteran-support operations and platform reliability duties are distinct and owned.
- [ ] Queue/dead-letter/provider-health/`PROVIDER_UNKNOWN`/notification failure paths are observable and operable.
- [ ] Provider enable/disable/manual fallback/reconciliation runbooks exist.
- [ ] Overdue Follow-Up and resource freshness have operational owners.
- [ ] Scale response begins with measurement/backpressure/capacity/query fixes, not architecture rewrite.
- [ ] No 24/7/provider-availability claim exceeds actual D-009/contracts.

### Incident response
- [ ] Incident process covers security, tenant isolation, session revoke, domain/idempotency integrity, queue/event failures, provider/notification failures, and operational support failures.
- [ ] Containment preserves open Cases/Requests/Attempts/Notifications rather than deleting history.
- [ ] Ongoing veteran-support impact is assessed during technical incidents.
- [ ] Evidence includes idempotency, events, Settlement/FulfillmentAttempt/Follow-Up state as relevant.
- [ ] Restore/replay verification includes duplicate-side-effect protection.
- [ ] Spec gaps return to SUAS-specs.
- [ ] No legal notification deadline/severity timer is invented.

### Recovery
- [ ] Backup/restore process is exercised for target release environment.
- [ ] D-024 owns RTO/RPO.
- [ ] Restored queues/provider Attempts/idempotency history are reconciled before workers resume risky external effects.

---

## 3. Non-goals

No cloud/DB/queue vendor selection, no 24/7 staffing claim, no legal-deadline invention, no Kubernetes requirement, no auto-dispatch emergency behavior.

---

## 4. Exit criteria

SPEC-013 may be accepted after dependencies when environment topology, runbook ownership, incident evidence/recovery semantics, and vendor-neutral operations align across required specs.

**Implementation handoff:** none until SPEC-016 release.
