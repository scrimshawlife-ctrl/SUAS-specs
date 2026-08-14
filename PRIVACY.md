# PRIVACY.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Privacy posture
SUAS should collect only the data needed to coordinate support for the active pilot use cases. Privacy controls must work alongside consent controls, not as substitutes.

## Data minimization
- Capture the minimum information needed to understand a support need.
- Avoid collecting medical detail unless explicitly necessary for a non-clinical coordination note and authorized by policy.
- Prefer structured categories over free-text where practical.
- Restrict access to contact and location data.

## PII handling
Veteran names, contact details, location details, trusted-circle relationships, and fulfillment evidence may constitute sensitive personal information. Access should be role-gated, consent-gated, and logged.

## Retention posture
- Active case data: retain while coordination is ongoing.
- Closed case records: retain according to approved policy `[DECISION_PENDING via ADR-021]`.
- Audit records: retain longer than operational views `[DECISION_PENDING via ADR-022]`.
- Notification exhaust: retain only as needed for delivery proof and audit.

## HIPAA note
`HIPAA_APPLICABILITY = DECISION_PENDING` under `ADR-023`. This bootstrap spec does not claim HIPAA status, coverage, or exemption.

## Clinical boundaries
SUAS is not a medical diagnosis tool and should not present clinical claims. Any health-adjacent observations should remain `[OBSERVED]` statements or operational notes rather than inferred diagnoses.

## Decision-pending items
- `ADR-021`: closed-record retention period.
- `ADR-022`: audit retention period.
- `ADR-023`: HIPAA applicability assessment process.
- `ADR-024`: de-identification rules for analytics exports.

## Not computable items
- [NOT_COMPUTABLE] Whether a given external partner is subject to specific healthcare privacy obligations without verified agreements and counsel.

Related specs: [CONSENT.md](CONSENT.md), [SECURITY.md](SECURITY.md), [ANALYTICS.md](ANALYTICS.md), [SETTLEMENT.md](SETTLEMENT.md), [DECISIONS.md](DECISIONS.md).
