# CASES.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Case purpose
A `SupportCase` is the coordination envelope around one veteran during a bounded support episode. It owns coordination state, oversight, and cross-request context.

## State machine
Exact states:
`OPEN -> TRIAGED -> ASSIGNED -> ACTIVE -> FOLLOWUP -> RESOLVED -> CLOSED`

## State definitions
- `OPEN`: case created from signal or manual intake.
- `TRIAGED`: needs reviewed and initial response path selected.
- `ASSIGNED`: coordinator ownership and next actions assigned.
- `ACTIVE`: at least one service request or active coordination task underway.
- `FOLLOWUP`: fulfillment completed or paused while monitoring post-service outcomes.
- `RESOLVED`: coordination objectives met pending administrative closure.
- `CLOSED`: final settled state with retention policy applied.

## Transition guidance
- `OPEN` -> `TRIAGED` when initial review is complete.
- `TRIAGED` -> `ASSIGNED` when an accountable owner is set.
- `ASSIGNED` -> `ACTIVE` when one or more service requests are operational.
- `ACTIVE` -> `FOLLOWUP` when active fulfillment concludes and monitoring begins.
- `FOLLOWUP` -> `RESOLVED` when follow-up criteria are satisfied.
- `RESOLVED` -> `CLOSED` when settlement and closure checks pass.

## Ownership
Every open case should have a clearly accountable coordinator or designated owner. Ownership changes must be auditable and should not erase prior accountability.

## Re-entry rules
A case in `FOLLOWUP` or `RESOLVED` may reopen operational work by creating or reactivating service requests and moving back to `ACTIVE` if new observed needs arise. [INFERRED] The original case may be reused only when policy allows continuity; otherwise a new case may be preferable.

## Non-substitutes
A case is not a referral, not a single request, and not a fulfillment artifact.

Related specs: [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [REFERRALS.md](REFERRALS.md), [FOLLOWUP.md](FOLLOWUP.md), [SETTLEMENT.md](SETTLEMENT.md).
