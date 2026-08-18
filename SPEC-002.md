# SPEC-002.md — Consent, privacy, safety, security owner review worksheet (SUAS v0.1)

**Status:** `BLOCKED_BY_SPEC_001` / `draft` / not accepted / not released  
**Dependency:** [SPEC-001.md](SPEC-001.md) must be owner-accepted before SPEC-002 may be accepted.  
**Roadmap item:** [ROADMAP.md](ROADMAP.md) SPEC-002  
**Specification owner:** `@scrimshawlife-ctrl`  
**Related:** [CONSENT.md](CONSENT.md), [PRIVACY.md](PRIVACY.md), [SAFETY.md](SAFETY.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [SECURITY.md](SECURITY.md), [COMPLIANCE.md](COMPLIANCE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [TESTING.md](TESTING.md)

This worksheet prepares SPEC-002 owner review. It does not bypass SPEC-001, does not accept any file, and does not authorize implementation.

---

## 1. Objective

Accept the v0.1 boundaries for:

- first-class Consent Grants and revocation;
- minimum-necessary collection/disclosure;
- Trusted Circle visibility;
- red-state safety behavior and standing safety non-goals;
- security controls and threat model;
- provider-side fulfillment disclosure/security boundaries;
- the rule that compliance artifacts are registers/controls, not unsupported legal claims.

---

## 2. Files in scope

| File | SPEC-002 role |
|---|---|
| [CONSENT.md](CONSENT.md) | Consent object, evaluation-at-use, revocation, third-party/provider disclosure basis |
| [PRIVACY.md](PRIVACY.md) | Data minimization, collection/disclosure boundaries, provider projection |
| [SAFETY.md](SAFETY.md) | Red-state behavior, no automated emergency dispatch, no safety-critical generative AI |
| [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md) | Membership lifecycle and consent-dependent visibility |
| [SECURITY.md](SECURITY.md) | Security controls/threats, tenant isolation, provider/webhook boundary |

Supporting but not accepted by this worksheet unless separately listed in the roadmap stage:

- [COMPLIANCE.md](COMPLIANCE.md) remains a compliance register and counsel-review input; it does not make SUAS compliant.
- [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md) remains a later detailed provider contract under SPEC-009. SPEC-002 accepts only the consent/privacy/security boundary that later adapters must obey.
- [TESTING.md](TESTING.md) remains later acceptance under SPEC-012; this worksheet records required evidence expectations only.

---

## 3. Dependency rule

SPEC-002 status stays `BLOCKED_BY_SPEC_001` until SPEC-001 is owner-accepted.

An agent may repair objective contradictions in SPEC-002 files while blocked, but must not:

- tick this worksheet;
- mark SPEC-002 accepted;
- claim SPEC-001 is accepted;
- authorize implementation.

---

## 4. Owner checklist

Every item starts unchecked. Only `@scrimshawlife-ctrl` may mark completion after SPEC-001 acceptance.

### 4.1 Consent

- [ ] Consent is first-class, purpose-scoped, revocable, versioned, and audited; it is not a boolean.
- [ ] Missing/revoked/expired grant denies future third-party use unless an explicitly accepted system basis applies.
- [ ] Consent is evaluated at the moment of use, including queued notifications/provider work.
- [ ] Referral disclosure and provider fulfillment disclosure require matching purpose/grantee/data scope.
- [ ] Provider rerouting/replacement re-evaluates consent for the new grantee.
- [ ] Provider terms/SDK behavior cannot expand SUAS consent scope.
- [ ] Historical Consent/Audit records remain distinct from current permission.

### 4.2 Privacy

- [ ] Data minimization is accepted regardless of HIPAA applicability.
- [ ] SSN, DD-214/service-record dumps, diagnoses, medical history, continuous GPS, and full contact-list import are not collected by default.
- [ ] One-time purpose-scoped location may be used only for specified workflows and authorized recipients.
- [ ] External providers receive capability-specific minimum projections, not whole Support Cases by default.
- [ ] Case Notes, Check-In responses, Support Signal basis, Trusted Circle data, and audit history are excluded from provider payloads unless an explicit accepted workflow requires them.
- [ ] Provider request/response bodies containing veteran data are not ordinary logs/telemetry.
- [ ] Provider-side retention/deletion behavior is not invented before provider/legal decisions exist.

### 4.3 Safety

- [ ] SUAS does not automate 911/PSAP/emergency dispatch.
- [ ] SUAS does not diagnose or claim to predict suicidality.
- [ ] `RED` prioritizes human review and surfaces only approved safety-resource copy.
- [ ] Trusted Contact alerts require exact active Consent Grant coverage and re-check before send.
- [ ] No generative model determines primary Support Signal, emergency intervention, trusted-contact notification decision, service qualification, or case closure.
- [ ] D-012 approved safety copy remains `DECISION_PENDING` unless separately closed.

### 4.4 Trusted Circle

- [ ] Membership alone grants no visibility.
- [ ] Suspension/removal/revocation prevents future use even if grants remain.
- [ ] Responders do not enumerate Trusted Contact addresses by default.
- [ ] Notification preferences do not grant consent.

### 4.5 Security

- [ ] Role + tenant + row + consent/system basis is the authorization model.
- [ ] Responder/admin MFA and revocable sessions remain required.
- [ ] Cross-tenant access is denied and tested.
- [ ] Provider credentials are server-side, scoped, rotatable/revocable, and absent from client bundles/logs.
- [ ] Provider/notification webhooks are authenticated and replay/duplicate safe.
- [ ] Provider responses cannot bypass canonical Service Request/Fulfillment state machines.
- [ ] External mutations use Fulfillment Attempt/idempotency semantics.
- [ ] Arbitrary user-controlled server-side provider destination URLs are not permitted.
- [ ] Unknown/ambiguous external outcomes reconcile before risky retry.

### 4.6 Legal/compliance boundary

- [ ] `HIPAA_APPLICABILITY = DECISION_PENDING` remains explicit.
- [ ] No HIPAA/SOC2/ISO/other compliance claim is created by these specs.
- [ ] D-006 and D-013 remain owner/counsel decisions.
- [ ] No provider agreement or vendor marketing claim is treated as legal/compliance evidence.

---

## 5. Cross-artifact invariants

SPEC-002 acceptance must preserve all of these:

1. Consent controls **disclosure/use**, not provider/domain state semantics.
2. Provider selection does not change the amount of data SUAS is allowed to disclose.
3. Manual coordination is not a privacy bypass; the same minimum-necessary/consent rules apply to phone/email/manual workflows.
4. Small pilot scale is not a safety/security exception.
5. Provider outage or ambiguous provider state must not cause automatic emergency dispatch, silent case closure, or duplicated fulfillment.
6. Auditability must not require logging sensitive provider payload bodies.
7. Safety resources/copy are presentation/coordination behavior, not a diagnosis or automated intervention.

---

## 6. Evidence expected before acceptance

SPEC-002 is a specification-acceptance stage, not implementation readiness. Owner review should still confirm that the contracts are testable.

Expected downstream evidence contracts include:

- consent revocation and evaluate-at-use cases;
- trusted-circle zero-visibility-without-grant cases;
- provider payload projection fixtures;
- invalid/unsigned provider webhook rejection;
- duplicate webhook/idempotency cases;
- cross-tenant isolation cases;
- red-state no-auto-dispatch cases;
- log fixtures proving sensitive provider/request bodies are absent.

Actual execution evidence is governed later by [TESTING.md](TESTING.md) and launch readiness stages.

---

## 7. Open decisions preserved

SPEC-002 must not close by implication:

- D-006 legal/HIPAA classification;
- D-007 retention durations;
- D-012 approved safety copy;
- D-013 counsel review of compliance register;
- D-017 through D-020 provider selection;
- provider-specific contractual retention/deletion/security obligations not yet known.

Use `DECISION_PENDING` / `NOT_COMPUTABLE`; do not invent values.

---

## 8. Exit criteria

After SPEC-001 is accepted, SPEC-002 may exit only when:

- the owner completes this checklist;
- all five in-scope files are mutually consistent;
- no provider integration path bypasses consent/privacy/security rules;
- safety non-goals remain explicit;
- compliance/legal unknowns remain accurately labeled;
- the owner marks the relevant artifacts/stage accepted under [VERSIONING.md](VERSIONING.md).

**Implementation handoff:** none until the applicable contracts are eventually released. SPEC-002 acceptance alone is not implementation authority.
