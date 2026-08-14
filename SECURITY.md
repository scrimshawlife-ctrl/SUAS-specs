# SECURITY.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Security objectives
Protect veteran data, preserve system integrity, prevent unauthorized coordination actions, and maintain auditable accountability for pilot operations.

## Threat model sketch
Primary threat categories include:
- unauthorized access to veteran data
- credential theft or session hijacking
- privilege escalation by internal users
- tampering with support-case or fulfillment records
- consent bypass
- notification misdelivery
- evidence artifact leakage

## Control baseline
- encrypt data in transit
- encrypt data at rest `[DECISION_PENDING implementation detail under ADR-017]`
- role-based and scope-based access checks
- immutable or append-only audit records `[DECISION_PENDING under ADR-018]`
- secrets stored outside the repository
- security logging for auth, access denial, export, and administrative changes

## Repository rule
No secrets, API keys, credentials, or partner tokens belong in specification documents. This repository must remain free of operational secrets.

## Pilot-specific considerations
[INFERRED] The small pilot size reduces scale pressure but does not reduce sensitivity. Trusted-circle data, location details, and support evidence require strict access control and notification discipline.

## Decision-pending items
- `ADR-017`: key management and encryption architecture.
- `ADR-018`: audit log immutability mechanism.
- `ADR-019`: vulnerability management cadence and tooling.
- `ADR-020`: secure evidence artifact storage model.

## Residual risk notes
- [NOT_COMPUTABLE] External partner security posture before executed agreements.
- [SPECULATIVE] Threat expansion if multi-county rollout occurs.

Related specs: [AUTH.md](AUTH.md), [PRIVACY.md](PRIVACY.md), [ADMIN.md](ADMIN.md), [OPERATIONS.md](OPERATIONS.md), [DECISIONS.md](DECISIONS.md).
