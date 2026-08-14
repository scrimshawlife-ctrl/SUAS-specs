# NOTIFICATIONS.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Notification scope
Notifications communicate check-in prompts, assignment updates, follow-up reminders, and case coordination events through approved channels.

## Allowed channels
- SMS
- push notification
- in-app notification

Email is not specified for MVP in this bootstrap set. [DECISION_PENDING via ADR-029] may revisit channel scope.

## Consent-gated delivery
Every outbound notification must be justified by a valid grant, role scope, or essential system access purpose. Trusted-circle notifications require explicit grant coverage.

## Notification types
- check-in reminder
- coordinator action needed
- responder assignment offer
- request status update
- follow-up reminder
- trusted-circle update
- admin security notice

## Delivery controls
- channel preference awareness
- quiet hours policy `[DECISION_PENDING via ADR-030]`
- template versioning
- delivery status tracking
- suppression after revocation or closure when appropriate

## Provider decision
Notification provider selection is `DECISION_PENDING` under `ADR-031`.

## Constraints
- no unconsented disclosure
- no automated emergency dispatch messaging
- no generative AI composed primary signal text

Related specs: [CONSENT.md](CONSENT.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [DISPATCH.md](DISPATCH.md), [OPERATIONS.md](OPERATIONS.md), [DECISIONS.md](DECISIONS.md).
