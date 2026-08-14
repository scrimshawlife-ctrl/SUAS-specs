# README.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

[OBSERVED] This repository is the canonical specification authority for Shut Up and Serve (SUAS).
[OBSERVED] Current release line: `0.1.0`.
[OBSERVED] Current phase: `SPECIFICATION_BOOTSTRAP`.

## Overview
Shut Up and Serve (SUAS) is a consent-governed veteran support coordination platform for a pilot serving 25-50 veterans in Santa Clara County, California. The MVP focuses on support coordination for food, transportation, temporary shelter, peer support, responder coordination, referrals, trusted-circle, follow-up, and settlement tracking.

SUAS-specs defines the product, domain, safety, privacy, and operational expectations that any SUAS implementation must satisfy before implementation authority can be released. [INFERRED] Early implementation work should treat these documents as controlling specifications rather than advisory notes.

## Canonical loop
`SIGNAL -> NEED -> CONSENT -> COORDINATION -> FULFILLMENT -> FOLLOW-UP -> SETTLEMENT`

The loop is elaborated in [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [CONSENT.md](CONSENT.md), [DISPATCH.md](DISPATCH.md), [FULFILLMENT.md](FULFILLMENT.md), [FOLLOWUP.md](FOLLOWUP.md), and [SETTLEMENT.md](SETTLEMENT.md).

## Current status
| Item | Value |
| --- | --- |
| Specification phase | SPECIFICATION_BOOTSTRAP |
| Implementation authority | NOT_YET_RELEASED |
| Pilot readiness | NOT_READY |
| Spec status | DRAFT |

## Core specification documents
- [PRODUCT.md](PRODUCT.md) - product scope, pilot framing, and non-goals
- [ARCHITECTURE.md](ARCHITECTURE.md) - modular monolith baseline and deployment assumptions
- [DOMAIN_MODEL.md](DOMAIN_MODEL.md) - entities, relationships, and loop walkthrough
- [DATA_MODEL.md](DATA_MODEL.md) - PostgreSQL schema sketches for core records
- [AUTH.md](AUTH.md) - authentication, roles, and access boundaries
- [CONSENT.md](CONSENT.md) - granular grants and revocation model
- [SAFETY.md](SAFETY.md) - safety levels, escalation, and human control rules
- [SECURITY.md](SECURITY.md) - threat model and control expectations
- [PRIVACY.md](PRIVACY.md) - minimization, retention, and PII handling
- [CHECKINS.md](CHECKINS.md) - check-in capture and structure
- [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md) - deterministic signal computation rules
- [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md) - trusted-circle membership and permissions
- [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md) - responder assignment and capacity handling
- [CASES.md](CASES.md) - SupportCase lifecycle and ownership
- [DISPATCH.md](DISPATCH.md) - coordination and dispatch workflow
- [RESOURCES.md](RESOURCES.md) - community resource catalog and matching
- [REFERRALS.md](REFERRALS.md) - referral workflow and outcomes
- [FULFILLMENT.md](FULFILLMENT.md) - fulfillment evidence and confirmation
- [FOLLOWUP.md](FOLLOWUP.md) - post-fulfillment touchpoints and re-entry
- [SETTLEMENT.md](SETTLEMENT.md) - closure criteria and retention handoff

## Platform and governance documents
- [NOTIFICATIONS.md](NOTIFICATIONS.md) - consent-gated notifications and channel rules
- [ADMIN.md](ADMIN.md) - admin controls and audit expectations
- [PILOT.md](PILOT.md) - pilot parameters and success framing
- [ANALYTICS.md](ANALYTICS.md) - aggregate measurement rules
- [API.md](API.md) - API principles and versioning
- [EVENT_MODEL.md](EVENT_MODEL.md) - domain event catalog and event envelope
- [TESTING.md](TESTING.md) - specification compliance testing approach
- [DEPLOYMENT.md](DEPLOYMENT.md) - environment tiers and release assumptions
- [OPERATIONS.md](OPERATIONS.md) - runbooks, observability, and support operations
- [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md) - incident classification and escalation
- [VERSIONING.md](VERSIONING.md) - semver policy and release governance
- [ROADMAP.md](ROADMAP.md) - SPEC-001 through SPEC-015 roadmap
- [STATUS.md](STATUS.md) - current per-spec status summary
- [AGENTS.md](AGENTS.md) - automation boundaries and human authority
- [GLOSSARY.md](GLOSSARY.md) - shared terminology
- [CONTRIBUTING.md](CONTRIBUTING.md) - contribution and review process
- [CHANGELOG.md](CHANGELOG.md) - release history
- [DECISIONS.md](DECISIONS.md) - ADR log for `DECISION_PENDING` items
- [SPEC_AUDIT.md](SPEC_AUDIT.md) - bootstrap audit, unresolved items, and next-run guidance

## Normative constraints
- [OBSERVED] SUAS is not a crisis-prediction application.
- [OBSERVED] SUAS is not an EHR.
- [OBSERVED] SUAS is not a medical diagnosis tool.
- [OBSERVED] SUAS does not automate 911 dispatch.
- [OBSERVED] Generative AI must not be used as the primary signal source.
- [OBSERVED] AI cannot make safety-critical decisions.
- [OBSERVED] `HIPAA_APPLICABILITY = DECISION_PENDING`.
- [OBSERVED] Partner placeholders remain `PARTNER_ORG_001` until verified agreements exist.

## Release authority
Any future SUAS implementation must conform to released specifications from this repository. [INFERRED] Until a release is explicitly marked authoritative beyond bootstrap, implementers should assume unresolved decisions remain binding constraints rather than optional flexibility.

Related specs: [STATUS.md](STATUS.md), [VERSIONING.md](VERSIONING.md), [DECISIONS.md](DECISIONS.md), [SPEC_AUDIT.md](SPEC_AUDIT.md).
