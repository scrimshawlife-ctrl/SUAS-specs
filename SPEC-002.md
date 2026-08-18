# SPEC-002.md — Owner acceptance record: consent, privacy, safety, security

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the v0.1 consent, privacy, safety, Trusted Circle, and security boundaries.

Accepted invariants:

- Consent is first-class, purpose-scoped, revocable, versioned, audited, and evaluated at use time.
- Provider/referral/manual disclosures use minimum-necessary projections and do not inherit broader access from provider terms or SDKs.
- Trusted Circle membership alone grants no visibility.
- SUAS does not automate emergency dispatch, diagnose, or claim suicidality prediction.
- `RED` prioritizes human review; official safety copy remains unavailable until D-012 closes.
- No generative model controls primary Support Signal, emergency intervention, trusted-contact notification decision, service qualification, or case closure.
- Authorization is role + tenant + row + consent/documented basis.
- Provider/webhook credentials and payloads stay behind server-side security boundaries; duplicate/replay/ambiguous external effects are handled safely.
- Compliance artifacts remain registers/controls, not compliance claims.

D-006, D-007, D-012, D-013, and provider-specific legal/security obligations remain unresolved for production and are governed by the v0.1.0 release decision ledger.
