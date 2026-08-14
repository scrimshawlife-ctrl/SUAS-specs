# PRODUCT.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Product vision
SUAS is a consent-governed support coordination platform for veterans who need help navigating time-bounded and practical support situations. The MVP scope centers on coordination, not diagnosis, prediction, or adjudication.

## Pilot scope
- [OBSERVED] Geography: Santa Clara County, California.
- [OBSERVED] Pilot size: 25-50 veterans.
- [OBSERVED] Initial partner placeholder: `PARTNER_ORG_001` only.
- [INFERRED] The pilot should prefer a manageable operating model over geographic expansion.

## What SUAS is
SUAS captures a support signal, translates it into explicit needs, collects granular consent, coordinates human responders and resources, records fulfillment, schedules follow-up, and settles the case when support work is complete.

## What SUAS is not
- [OBSERVED] Not a crisis-prediction application.
- [OBSERVED] Not an electronic health record.
- [OBSERVED] Not a medical diagnosis tool.
- [OBSERVED] Not automated emergency dispatch.
- [OBSERVED] Not a benefits eligibility engine for VA, Medi-Cal, or other programs.
- [OBSERVED] Not a billing platform in MVP; settlement billing status remains `FUTURE`.

## Epistemic posture
- Use `OBSERVED` for confirmed inputs, user-entered facts, or verified operational records.
- Use `INFERRED` for deterministic transformations from observed facts.
- Use `SPECULATIVE` for planning assumptions that require validation.
- Use `NOT_COMPUTABLE` for items that cannot be determined from available information.

## MVP service categories
1. Food support.
2. Transportation support.
3. Temporary shelter support.
4. Peer support.
5. Responder coordination.
6. Referrals.
7. Trusted-circle participation.
8. Follow-up.
9. Settlement tracking.

## Product boundaries
Support coordination revolves around the canonical loop `SIGNAL -> NEED -> CONSENT -> COORDINATION -> FULFILLMENT -> FOLLOW-UP -> SETTLEMENT`. The loop does not authorize autonomous outreach beyond granted permissions in [CONSENT.md](CONSENT.md) or safety pathways in [SAFETY.md](SAFETY.md).

## Success posture
[INFERRED] A successful MVP produces reliable case coordination records, deterministic signal handling, auditable consent artifacts, and measurable follow-up completion without making unsupported clinical or legal claims.

## Exclusions from bootstrap
- [NOT_COMPUTABLE] Verified partner SLAs.
- [NOT_COMPUTABLE] VA or Medi-Cal eligibility logic.
- [NOT_COMPUTABLE] Clinical efficacy claims.
- [SPECULATIVE] Long-term expansion outside Santa Clara County.

Related specs: [README.md](README.md), [PILOT.md](PILOT.md), [CONSENT.md](CONSENT.md), [SAFETY.md](SAFETY.md), [GLOSSARY.md](GLOSSARY.md).
