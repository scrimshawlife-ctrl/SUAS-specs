# FULFILLMENT.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Fulfillment purpose
A `Fulfillment` record documents that a service request was delivered, partially delivered, attempted, or otherwise actioned in a way that matters to case coordination.

## Confirmation flow
1. Assignment is active.
2. Responder records work performed or attempted.
3. Evidence is attached or summarized.
4. Coordinator or veteran confirms when policy requires it.
5. Request transitions from `FULFILLED` to `CONFIRMED` and then `CLOSED` when complete.

## Evidence examples
- delivery timestamp
- responder observation
- veteran confirmation
- pickup or drop-off note
- referral acceptance note
- follow-up scheduling note

## State guidance
- `IN_PROGRESS` -> `FULFILLED` when work appears complete from operational evidence.
- `FULFILLED` -> `CONFIRMED` when veteran or coordinator confirmation criteria are met.
- `CONFIRMED` -> `CLOSED` when no further action is pending on that request.

## Partial or failed fulfillment
If a request is only partly met or cannot be completed, the system should document the reason and move through the appropriate canonical state such as `UNFULFILLABLE`, `DECLINED`, `EXPIRED`, or back to `MATCHING`.

## Audit and privacy
Evidence must remain minimal and operational. No unsupported clinical interpretations should be embedded in fulfillment notes.

Related specs: [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [FOLLOWUP.md](FOLLOWUP.md), [SETTLEMENT.md](SETTLEMENT.md), [PRIVACY.md](PRIVACY.md).
