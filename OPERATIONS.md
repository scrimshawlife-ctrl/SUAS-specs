# OPERATIONS.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Operations scope
Operations covers the human and technical practices needed to run the pilot safely and consistently.

## Runbook areas
- auth and access issue handling
- notification delivery failures
- case triage backlog management
- responder assignment failures
- referral routing issues
- fulfillment evidence review
- closure and retention jobs

## Observability
The implementation should provide logs, metrics, and audit views sufficient to understand case throughput, notification health, access anomalies, and workflow failures.

## Alerting
Alerting should focus on operational failures such as database availability, authentication disruption, notification delivery collapse, and backlog growth that threatens pilot service quality.

## Decision-pending items
- `ADR-041`: observability stack.
- `ADR-042`: on-call ownership and escalation model.
- `ADR-043`: incident ticketing and runbook platform.

## Human operations note
[INFERRED] The pilot likely needs lightweight but disciplined runbooks because staff capacity is limited and unresolved safety events require accountable response.

## Constraints
- no automated emergency dispatch
- no silent failure of consent enforcement
- no operations practice that hides audit gaps

Related specs: [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [SECURITY.md](SECURITY.md), [ADMIN.md](ADMIN.md), [DECISIONS.md](DECISIONS.md).
