# FOLLOWUP.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Follow-up purpose
`FollowUp` ensures that fulfillment results are checked, additional needs are surfaced, and the veteran has an opportunity to confirm whether support actually helped.

## Scheduling rules
Follow-up timing should depend on request category, urgency, and consented contact methods. [DECISION_PENDING via ADR-027] default cadence policy remains unresolved.

## Touchpoint types
- veteran confirmation message
- coordinator call or message
- trusted-circle check if consent allows
- responder outcome review
- re-check-in prompt

## Re-signal triggers
A follow-up may trigger a new `SupportSignal` if observed conditions indicate an unmet or worsening need. The signal must still be computed deterministically according to [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md).

## Re-opening conditions
A case may move from `FOLLOWUP` or `RESOLVED` back toward active work when:
- a follow-up reveals an unmet service need
- the veteran requests additional help
- a confirmed fulfillment fails to hold
- a trusted-circle member provides authorized observed information

## Completion
Follow-up is complete when touchpoints are executed or no longer appropriate, outcomes are recorded, and either the case returns to active coordination or proceeds to settlement.

## Constraints
- follow-up notifications are consent-gated
- no coercive outreach loops
- no inference of medical status from non-response

Related specs: [CHECKINS.md](CHECKINS.md), [CONSENT.md](CONSENT.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [SETTLEMENT.md](SETTLEMENT.md), [DECISIONS.md](DECISIONS.md).
