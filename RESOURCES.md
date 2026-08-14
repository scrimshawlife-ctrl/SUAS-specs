# RESOURCES.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Resource catalog purpose
The resource catalog represents internal or external options that may help satisfy service requests. It is a coordination aid, not a guarantee of eligibility or availability.

## Resource types
- food provider
- transportation option
- temporary shelter option
- peer support resource
- responder team
- referral destination
- follow-up support channel

## Core attributes
- resource id
- resource type
- organization placeholder or verified identity
- county or service area
- availability window
- intake method
- contact path
- eligibility notes `[OBSERVED only when verified]`
- restrictions `[OBSERVED only when verified]`

## Matching rules
Matching should compare request category, urgency, location relevance, operating hours, and consent constraints. Any claim that a veteran qualifies for a third-party service remains `[NOT_COMPUTABLE]` until the authorized provider confirms it.

## Availability
Availability may be `[OBSERVED]` if directly updated by an authorized human or verified feed. It is `[SPECULATIVE]` if inferred from stale data and should be labeled accordingly in operations.

## Placeholder partner policy
Until agreements are verified, partner references should use `PARTNER_ORG_001` as placeholder notation.

## Governance
Resource entries need review cadence, deactivation handling, and provenance tracking to avoid stale routing.

Related specs: [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [REFERRALS.md](REFERRALS.md), [PILOT.md](PILOT.md), [PRIVACY.md](PRIVACY.md).
