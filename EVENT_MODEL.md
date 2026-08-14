# EVENT_MODEL.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Event model purpose
The event model defines the domain-level facts that should be emitted for audit, analytics, and integration boundaries.

## Event envelope
Each event should include:
- event id
- event type
- occurred at timestamp
- actor id or system actor
- aggregate type and id
- correlation id
- causation id
- payload
- provenance label where relevant

## Domain event catalog
- `check_in.submitted`
- `support_signal.computed`
- `support_signal.overridden`
- `support_case.opened`
- `support_case.state_changed`
- `service_request.created`
- `service_request.state_changed`
- `referral.created`
- `assignment.created`
- `assignment.accepted`
- `fulfillment.recorded`
- `follow_up.scheduled`
- `settlement.recorded`
- `consent_grant.created`
- `consent_grant.revoked`
- `notification.sent`

## Decision-pending items
- `ADR-004`: event bus or in-process eventing approach.
- `ADR-037`: event sourcing versus audit-log-first architecture.
- `ADR-038`: retention and replay policy for events.

## Audit relationship
Even if full event sourcing is not adopted, mutating operations should emit durable audit events. [INFERRED] Audit completeness is mandatory; event sourcing remains optional pending decision.

## Analytics relationship
Analytics should read from stable event definitions rather than fragile screen-level logs.

Related specs: [DATA_MODEL.md](DATA_MODEL.md), [ANALYTICS.md](ANALYTICS.md), [ADMIN.md](ADMIN.md), [DECISIONS.md](DECISIONS.md).
