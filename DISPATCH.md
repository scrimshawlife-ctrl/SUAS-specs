# DISPATCH.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Dispatch meaning
In SUAS, dispatch means human-coordinated assignment and routing of support activity. It does not mean emergency dispatch automation.

## Coordination flow
1. Coordinator reviews a `SupportCase` and `ServiceRequest`.
2. Consent grants are evaluated.
3. Resource and responder options are matched.
4. A human selects or approves the assignment.
5. Notifications are sent through allowed channels.
6. Assignment progress is monitored.

## Human approval gates
- approval before outreach to a new responder or partner
- approval before sharing sensitive location detail
- approval before manual external escalation
- approval before changing a request to `ESCALATED` when safety review is involved

## Prohibitions
- no automated 911 dispatch
- no autonomous AI dispatch decisions
- no bypass of consent checks
- no unlogged reassignment

## Dispatch artifacts
A dispatch record should include request id, responder id, dispatcher id, dispatch timestamp, share scope, consent basis, and status outcome.

## Relationship to service states
Dispatch usually operates while requests are in `MATCHING`, `ASSIGNED`, `ACCEPTED`, or `IN_PROGRESS`. Failure paths may transition to `DECLINED`, `EXPIRED`, `UNFULFILLABLE`, or `ESCALATED`.

## Pilot operational note
[INFERRED] For a small pilot, clear coordinator control and queue visibility matter more than route optimization sophistication.

Related specs: [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [SAFETY.md](SAFETY.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [FULFILLMENT.md](FULFILLMENT.md).
