# RESPONDER_WORKFLOWS.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Responder roles
Responders execute or assist with service requests under coordinator oversight and within consent boundaries. A responder may be a staff member, contractor, volunteer, or placeholder partner actor depending on future operating design.

## Assignment flow
1. Coordinator reviews case and request.
2. Request enters `MATCHING`.
3. Candidate responder is identified.
4. Assignment is offered or created.
5. Responder accepts or declines.
6. Work progresses to `IN_PROGRESS` with evidence capture.

## Capacity handling
Responder capacity should consider role, service category, schedule, geography, and active workload. [DECISION_PENDING via ADR-026] The algorithmic ranking approach remains unresolved, but human override must remain possible.

## Dispatch rules
- no automated 911 dispatch
- no hidden reassignment without audit
- no assignment outside granted scope
- no closure without fulfillment or documented disposition

## Escalation paths
When a responder cannot accept a request, the request may return to `MATCHING`, become `ESCALATED`, or be marked `UNFULFILLABLE` depending on policy and documented rationale.

## Audit fields
Assignments should record who assigned, who accepted, timestamps, state changes, and any observed blockers. Responder notes remain operational, not clinical.

## Pilot assumptions
[INFERRED] The pilot may operate with limited responder supply, so transparent queueing and coordinator control are more important than automated optimization.

Related specs: [DISPATCH.md](DISPATCH.md), [CASES.md](CASES.md), [FULFILLMENT.md](FULFILLMENT.md), [RESOURCES.md](RESOURCES.md), [DECISIONS.md](DECISIONS.md).
