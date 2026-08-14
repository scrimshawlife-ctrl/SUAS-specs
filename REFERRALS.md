# REFERRALS.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Referral purpose
A `Referral` captures a directed handoff or recommendation from SUAS coordination to a specific resource, responder group, or external placeholder partner.

## Creation flow
1. Coordinator identifies a service request requiring a referral.
2. Consent scope for sharing is verified.
3. Referral target is chosen.
4. Referral payload is created with minimal necessary data.
5. Referral status is tracked until outcome is known.

## Referral fields
- related support case
- related service request
- destination resource or organization
- referral reason
- shared data scope
- sent timestamp
- status
- outcome notes

## Relationship to service requests
A referral does not replace the `ServiceRequest`; it supports fulfilling it. One request may require multiple referrals, and one referral may fail without closing the request.

## Outcome tracking
Possible outcomes may include accepted, declined, expired, information requested, or completed. These are referral-local outcomes and must not overwrite the canonical service-request states without explicit transition logic.

## Constraints
- no unconsented data sharing
- no unsupported eligibility claims
- no assumption that a referral equals fulfillment

## Pilot placeholder note
External referrals should use `PARTNER_ORG_001` until real partner identities and agreements are verified.

Related specs: [RESOURCES.md](RESOURCES.md), [CONSENT.md](CONSENT.md), [FULFILLMENT.md](FULFILLMENT.md), [CASES.md](CASES.md).
