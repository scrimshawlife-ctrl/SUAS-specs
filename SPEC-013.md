# SPEC-013.md — Owner acceptance record: deployment, operations, incident/recovery

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the environment, day-2 operations, incident-response, and recovery contracts.

Accepted requirements:

- LOCAL/TEST/STAGING/PRODUCTION are isolated for data, secrets, durable work, provider configuration, telemetry, and identities.
- No production veteran data may enter non-production.
- Production topology must support stateless application instances and durable workers without domain rewrite.
- Provider/notification webhook ingress is authenticated; secrets remain deployment-only.
- Veteran-support operations and platform reliability duties are distinct and owned.
- Queue/dead-letter/provider-health/`PROVIDER_UNKNOWN`/notification failure, manual fallback, Follow-Up, resource freshness, and recovery paths are observable and operable.
- Incident response covers tenant/security/session/idempotency/event/queue/provider/notification/support failures while preserving history and assessing veteran-support impact.
- Restore/replay procedures preserve duplicate-side-effect protection and reconcile pending external work before risky resume.
- Spec gaps return to `SUAS-specs`.

D-001, D-005, D-009, D-022, D-023, and D-024 remain deferred for this implementation-only release. No production deployment, staffing promise, legal deadline, vendor, Kubernetes requirement, or recovery target is authorized by this acceptance.
