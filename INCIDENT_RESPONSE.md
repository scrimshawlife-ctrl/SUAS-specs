# INCIDENT_RESPONSE.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Incident response scope
Incident response covers technical, privacy, security, and operational incidents affecting SUAS pilot operation.

## Classification
- Severity 1: system outage or material safety/process failure requiring immediate human coordination.
- Severity 2: degraded service, major workflow blockage, or significant security concern.
- Severity 3: limited-scope defect or workflow error with workaround.
- Severity 4: informational issue or low-impact defect.

## Escalation contacts
Named contacts are `[NOT_COMPUTABLE]` at bootstrap until staffing and governance are confirmed. Contact roster definition remains required before pilot launch.

## Core procedure
1. detect incident
2. classify severity
3. notify accountable humans
4. contain impact
5. preserve evidence
6. communicate status
7. remediate and review

## Safety rule
No incident workflow may automatically dispatch emergency services. Human responders must decide any external emergency action.

## Post-incident review
A post-incident review should document timeline, impact, root cause, mitigations, and spec implications.

## Decision-pending items
- `ADR-044`: formal severity matrix ownership.
- `ADR-045`: communication channel and paging approach.

Related specs: [SAFETY.md](SAFETY.md), [OPERATIONS.md](OPERATIONS.md), [SECURITY.md](SECURITY.md), [PILOT.md](PILOT.md), [DECISIONS.md](DECISIONS.md).
