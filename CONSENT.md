# CONSENT.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Consent-first principles
Consent is a first-class, granular set of grants, not a single boolean. The system must record what was authorized, by whom, for what purpose, for which parties, and for what time range.

## Grant model
Each consent grant should include:
- subject veteran identifier
- grant type
- approved recipients or recipient classes
- allowed actions
- scope boundaries
- effective time window
- capture channel and witness metadata when applicable
- revocation status and reason

## Example grant categories
- view case details
- contact veteran by channel
- contact trusted-circle member
- share service request details with `PARTNER_ORG_001`
- notify peer supporter
- disclose location for fulfillment
- schedule follow-up reminders

## Lifecycle
1. Grant requested.
2. Grant explained in plain language.
3. Grant accepted or declined.
4. Grant activated.
5. Grant reviewed when scope changes.
6. Grant revoked or expired.
7. Historical record retained for audit.

## Revocation rules
Revocation must stop future access or future notifications that rely on the revoked grant. [INFERRED] Historical actions performed while a grant was valid remain in the audit log, but new coordination activity must re-evaluate active grants.

## What each grant unlocks
No request routing, trusted-circle notification, or responder disclosure should occur unless the relevant grant authorizes that action. A veteran may allow transportation coordination while declining trusted-circle outreach or peer support.

## Constraints
- Consent does not authorize automated emergency dispatch.
- Consent does not transform SUAS into an EHR.
- Consent records must be reviewable by authorized humans.
- Safety-critical human interventions may still require operational judgment, but AI cannot make that judgment.

## Decision-pending items
- `ADR-014`: canonical consent taxonomy and grant naming.
- `ADR-015`: evidence format for verbal consent capture.
- `ADR-016`: expiration defaults and renewal prompts.

Related specs: [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [PRIVACY.md](PRIVACY.md), [SAFETY.md](SAFETY.md), [DECISIONS.md](DECISIONS.md).
