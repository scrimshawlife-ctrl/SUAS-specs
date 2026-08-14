# API.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## API principles
The SUAS API should be explicit, versioned, and aligned to domain concepts rather than implementation shortcuts.

## Versioning
- Base path: `/v1/`
- Backward-incompatible changes require a version bump.
- Spec release and API release should remain traceable through [VERSIONING.md](VERSIONING.md).

## Resource naming
Prefer nouns that match domain entities:
- `/v1/check-ins`
- `/v1/support-signals`
- `/v1/support-cases`
- `/v1/service-requests`
- `/v1/referrals`
- `/v1/assignments`
- `/v1/fulfillments`
- `/v1/follow-ups`
- `/v1/settlements`

## Auth patterns
Authenticated requests should carry actor identity, role context, and authorization scope. Consent checks remain server-side and auditable.

## Design expectations
- idempotent creation support where appropriate
- explicit state transition endpoints or commands
- validation errors that do not leak sensitive data
- audit event emission for mutating operations
- pagination and filtering with policy-aware defaults

## Decision-pending items
- `ADR-034`: framework and routing stack.
- `ADR-035`: REST-only versus mixed API style.
- `ADR-036`: optimistic concurrency and versioning strategy.

## Constraints
- no unsafe bulk export without policy checks
- no state names outside the canonical lists
- no endpoint semantics that imply autonomous safety decisions

Related specs: [AUTH.md](AUTH.md), [CONSENT.md](CONSENT.md), [EVENT_MODEL.md](EVENT_MODEL.md), [TESTING.md](TESTING.md), [DECISIONS.md](DECISIONS.md).
