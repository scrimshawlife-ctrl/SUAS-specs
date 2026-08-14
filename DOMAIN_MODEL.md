# DOMAIN_MODEL.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Domain entities
- `CheckIn`: a structured veteran-submitted or staff-assisted status update.
- `SupportSignal`: the deterministic interpretation of check-in or observed need conditions into `GREEN`, `YELLOW`, `ORANGE`, or `RED`.
- `SupportCase`: the coordination record around one veteran for a bounded support episode.
- `ServiceRequest`: a specific need within a case, such as transportation or food support.
- `Referral`: a routed handoff to an internal or external resource or organization.
- `Assignment`: a responsibility link between a service request and a human responder, coordinator, or peer supporter.
- `Fulfillment`: evidence that a service request was delivered or attempted.
- `FollowUp`: a scheduled or completed post-fulfillment touchpoint.
- `Settlement`: the closure artifact explaining why a case can move from active coordination to closure.

## Core distinctions
`SupportCase` is not the same as `ServiceRequest`.
- A `SupportCase` groups coordination work for one veteran over a bounded time window.
- A `ServiceRequest` captures one concrete need, state machine, and fulfillment path.
- One case may contain multiple service requests.
- One referral may satisfy part of one service request or redirect it.

## Relationship sketch
- One veteran can have many `CheckIn` records.
- One `CheckIn` may generate zero or one primary `SupportSignal` plus supporting observations.
- One veteran can have many `SupportCase` records over time.
- One `SupportCase` can contain many `ServiceRequest`, `Referral`, `Assignment`, `Fulfillment`, `FollowUp`, and one or more `Settlement` drafts over its lifecycle.
- `TrustedCircle` participants may receive notifications or contribute observations only when consent grants allow it.

## Canonical loop walkthrough
1. `SIGNAL`: a `CheckIn` or verified responder observation is recorded.
2. `NEED`: a coordinator identifies one or more concrete support needs.
3. `CONSENT`: granular grants authorize who may view, contact, coordinate, or notify.
4. `COORDINATION`: a `SupportCase` is managed and `ServiceRequest` records are created.
5. `FULFILLMENT`: one or more assignments result in service delivery or documented attempts.
6. `FOLLOW-UP`: the veteran or authorized contacts receive post-service touchpoints.
7. `SETTLEMENT`: the case is resolved and then closed when criteria are satisfied.

## State-bearing entities
- `SupportCase` states: `OPEN`, `TRIAGED`, `ASSIGNED`, `ACTIVE`, `FOLLOWUP`, `RESOLVED`, `CLOSED`.
- `ServiceRequest` states: `CREATED`, `SUBMITTED`, `TRIAGED`, `MATCHING`, `ASSIGNED`, `ACCEPTED`, `IN_PROGRESS`, `FULFILLED`, `CONFIRMED`, `CLOSED`, `CANCELLED`, `DECLINED`, `EXPIRED`, `UNFULFILLABLE`, `ESCALATED`.

## Epistemic notes
- Veteran-stated needs are `[OBSERVED]` as stated by the veteran, but suitability of a resource match is `[INFERRED]` until confirmed.
- External partner eligibility is `[NOT_COMPUTABLE]` unless verified through an authorized operational process.
- Any forecast about future risk remains outside MVP scope.

Related specs: [CASES.md](CASES.md), [CHECKINS.md](CHECKINS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [REFERRALS.md](REFERRALS.md), [FULFILLMENT.md](FULFILLMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [SETTLEMENT.md](SETTLEMENT.md).
