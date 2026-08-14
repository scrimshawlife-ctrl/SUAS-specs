# ARCHITECTURE.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Baseline architecture
[OBSERVED] The bootstrap architecture is a modular monolith backed by PostgreSQL. The intent is to keep domain boundaries explicit while avoiding premature distributed-system complexity for a 25-50 veteran pilot.

## Architectural layers
1. API layer: authenticated endpoints, request validation, serialization, versioning, and channel-specific adapters.
2. Domain layer: entities, state transitions, consent evaluation, signal computation, coordination policies, and audit event emission.
3. Persistence layer: PostgreSQL schemas, transaction boundaries, query models, and append-only audit records.

## Internal modules
- Identity and access.
- Consent and trusted-circle management.
- Check-ins and support signals.
- Cases and service requests.
- Referrals, assignments, and fulfillment.
- Follow-up and settlement.
- Notifications and audit.
- Administration and analytics.

## Deployment overview
[INFERRED] A single deployable application with background job capability is sufficient for pilot scope. External integrations should be adapter-based and isolated behind interfaces so partner-specific behavior can remain placeholder-driven until validated.

## Normative constraints
- PostgreSQL is the canonical relational store.
- The system must preserve deterministic support-signal computation; no generative AI in the primary signal path.
- Human approval gates remain mandatory for any safety escalation or responder dispatch path.
- No automated 911 dispatch is permitted.

## Decision-pending items
- `ADR-001`: application framework and language stack.
- `ADR-002`: background job runner and scheduler choice.
- `ADR-003`: deployment topology and hosting boundary.
- `ADR-004`: event bus or in-process eventing approach.
- `ADR-005`: file/blob storage approach for evidence artifacts.

## Architectural invariants
A `SupportCase` represents the coordination container around a veteran, while a `ServiceRequest` represents one actionable need within that case. This distinction must be preserved in all implementation layers and API shapes.

## Change guidance
[INFERRED] Architecture changes should prefer additive modules, explicit interfaces, and transactional consistency over broad rewrites. If scale or regulatory requirements later justify decomposition, this spec stack should be revised before implementation diverges.

Related specs: [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [API.md](API.md), [EVENT_MODEL.md](EVENT_MODEL.md), [DECISIONS.md](DECISIONS.md).
