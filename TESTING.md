# TESTING.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Testing scope
This repository contains specifications, not production code. Testing in this context means validating that future implementations conform to these documents.

## Test categories
- document consistency checks
- state-machine conformance tests
- API contract tests
- consent enforcement tests
- safety rule tests
- privacy and access control tests
- audit event completeness tests

## Acceptance criteria examples
- SupportCase transitions only use `OPEN`, `TRIAGED`, `ASSIGNED`, `ACTIVE`, `FOLLOWUP`, `RESOLVED`, `CLOSED`.
- ServiceRequest transitions only use canonical request states.
- `RED` signal handling requires human review.
- No automated 911 dispatch path exists.
- Consent revocation prevents future disclosures.

## Spec compliance posture
[INFERRED] An implementation should map every endpoint, screen, and workflow to one or more specification requirements with traceable test coverage.

## Documentation checks
Bootstrap validation should confirm file presence, README link integrity, forbidden phrase absence, and state vocabulary consistency.

## Non-goals
- no production runtime testing defined here
- no performance SLA claims at bootstrap
- no load-test thresholds before architecture decisions are resolved

Related specs: [README.md](README.md), [SAFETY.md](SAFETY.md), [CONSENT.md](CONSENT.md), [API.md](API.md), [SPEC_AUDIT.md](SPEC_AUDIT.md).
