# DEPLOYMENT.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Deployment posture
Deployment specifics remain `DECISION_PENDING`, but the spec assumes a controlled delivery process for a modular monolith backed by PostgreSQL.

## Environment tiers
- `dev`: local and integration development environment
- `staging`: pre-pilot validation environment
- `prod`: pilot-serving production environment

## Baseline expectations
- environment-specific configuration separation
- managed database backups
- deployment audit trail
- rollback procedure
- secrets managed outside the repository

## Decision-pending items
- `ADR-003`: hosting topology and platform.
- `ADR-039`: containerization and packaging approach.
- `ADR-040`: infrastructure as code strategy.

## Pilot notes
[INFERRED] The pilot should favor operational simplicity, explicit release gates, and reproducible environment configuration over advanced multi-region patterns.

## Constraints
- no production deployment before implementation authority is released
- no environment that bypasses audit logging for pilot traffic
- no partner-specific infrastructure assumptions without verified agreements

Related specs: [ARCHITECTURE.md](ARCHITECTURE.md), [OPERATIONS.md](OPERATIONS.md), [SECURITY.md](SECURITY.md), [STATUS.md](STATUS.md), [DECISIONS.md](DECISIONS.md).
