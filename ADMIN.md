# ADMIN.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Admin scope
Admin capabilities exist to configure the pilot, manage users, review audit trails, and maintain controlled operating parameters. Admin is a governance role, not an unrestricted observer role.

## Capabilities
- manage user accounts and role assignments
- review audit events
- manage resource catalog entries
- configure check-in cadence and templates
- configure signal rule versions under change control
- manage pilot-level settings and partner placeholders
- review notification delivery status

## Audit expectations
Admin actions must be especially well logged, including actor, before/after values, timestamp, and reason when appropriate. [INFERRED] Admin workflows should be designed assuming future review or external scrutiny.

## User management rules
- no silent privilege escalation
- no shared admin credentials
- no deletion that destroys required audit history
- no direct override of consent history without audit artifact

## Pilot configuration
Configuration should remain explicit for Santa Clara County pilot assumptions, service categories, and placeholder partner identifiers. Any move beyond `PARTNER_ORG_001` requires verified governance updates.

## Decision-pending items
- `ADR-032`: admin approval workflow for sensitive policy changes.
- `ADR-033`: break-glass access policy.

Related specs: [AUTH.md](AUTH.md), [SECURITY.md](SECURITY.md), [PILOT.md](PILOT.md), [EVENT_MODEL.md](EVENT_MODEL.md), [DECISIONS.md](DECISIONS.md).
