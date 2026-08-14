# AUTH.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Authentication scope
Authentication governs who may sign in to SUAS and under what assurance level. Authorization and consent evaluation remain separate concerns even when enforced together in the application.

## Baseline roles
- `veteran`: can submit check-ins, view their own cases and requests, manage consent, and review follow-up records exposed to them.
- `peer_supporter`: can participate in peer-support workflows within granted case scope.
- `responder`: can accept assignments and record fulfillment evidence within granted scope.
- `coordinator`: can triage cases, create service requests, manage referrals, and oversee follow-up.
- `admin`: can manage configuration, users, audit review, and policy settings.

## Authentication requirements
- Unique authenticated identity per actor.
- Session and token revocation support.
- Audit trail for sign-in, sign-out, credential reset, and privilege change.
- Least-privilege defaults.
- MFA posture is `DECISION_PENDING` under `ADR-010`.

## Authorization principles
Role membership alone does not unlock sensitive veteran data. Access must also satisfy case assignment scope and any applicable consent grants from [CONSENT.md](CONSENT.md).

## Decision-pending items
- `ADR-010`: MFA requirement and enrollment policy.
- `ADR-011`: SSO versus local identity provider approach.
- `ADR-012`: RBAC-only versus RBAC plus attribute constraints.
- `ADR-013`: session token format and lifetime.

## Prohibited shortcuts
- No shared responder accounts.
- No implicit admin access to all content without auditability.
- No bypass of consent evaluation for convenience workflows.
- No unaudited impersonation workflow.

## Pilot expectation
[INFERRED] The pilot should prefer operational simplicity, but identity assurance must still support auditability, revocation, and accountable access review.

Related specs: [CONSENT.md](CONSENT.md), [SECURITY.md](SECURITY.md), [ADMIN.md](ADMIN.md), [API.md](API.md), [DECISIONS.md](DECISIONS.md).
