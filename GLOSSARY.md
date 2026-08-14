# GLOSSARY.md — Canonical terms (SUAS v0.1)

**Authority:** This file is the terminology authority. All other specs must use these terms exactly.  
**SPEC-001 status:** `READY_FOR_REVIEW` (not `accepted`; not `released`; see [SPEC-001.md](SPEC-001.md))  
**Related:** [PRODUCT.md](PRODUCT.md), [README.md](README.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [COMPLIANCE.md](COMPLIANCE.md), [APIS.md](APIS.md), [ONBOARDING.md](ONBOARDING.md), [SPEC-001.md](SPEC-001.md), [FRICTION.md](FRICTION.md)

Terms are not interchangeable. Do not alias them in implementation or documentation.

---

## SUAS

The system identifier for Shut Up and Serve. SUAS is a consent-governed veteran support coordination platform. It is not a crisis-prediction app, not an EHR, not a medical diagnosis tool, and not an automated emergency-dispatch platform.

- Specification authority: `SUAS-specs` (`scrimshawlife-ctrl/SUAS-specs`)
- Implementation repository: `SUAS` (`https://github.com/scrimshawlife-ctrl/SUAS`)

---

## Veteran

The person whose need is being coordinated. The primary data subject and the only party who can grant or revoke Consent Grants about their own data, except where a documented legal process applies (`DECISION_PENDING`; do not invent). A Veteran has a `VeteranProfile` and may enroll in a Pilot.

MVP enrollment (`INFERRED`; D-016): self-attested veteran status plus a working email and/or phone via passwordless auth. No VA identity API, no DD-214 upload, no in-person proofing for the Santa Clara County pilot. See [ONBOARDING.md](ONBOARDING.md).

---

## Responder

A human coordinator who claims or is assigned a Support Case and performs coordination actions defined in [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md). A Responder belongs to an Organization via `OrganizationMembership` and has a `ResponderProfile`. A Responder is not a clinician by role definition. Clinical credentials are `NOT_COMPUTABLE` unless later specified.

---

## Trusted Circle

The set of Trusted Contacts a Veteran has invited and that have accepted, each bound by explicit Consent Grants. Membership in the Trusted Circle does not by itself grant visibility into Check-Ins, Support Signals, Support Cases, Service Requests, or location.

See [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md) and [CONSENT.md](CONSENT.md).

---

## Trusted Contact

A specific person in a Veteran's Trusted Circle. Has an invite/accept lifecycle, a relationship label, granular permissions, and notification preferences. All data access and alerts require a matching Consent Grant.

---

## Organization

A participating entity that employs or vets Responders, owns Resources, and may act as a Service Provider. Organization Administrator scope is limited to that Organization. Pilot partner organizations are placeholders (`PARTNER_ORG_001`, …) until named. See [DECISIONS.md](DECISIONS.md).

---

## Support Signal

A deterministic coordination label computed from a Check-In (or recorded as the input to coordination). Values: `GREEN`, `YELLOW`, `ORANGE`, `RED`. A Support Signal is **not** a diagnosis, risk score for suicidality, or clinical assessment. Computation is inspectable, versioned, unit-tested, and reproducible. No generative model may produce the primary signal.

See [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md).

---

## Check-In

A Veteran's completion (or partial completion) of a versioned questionnaire. A Check-In is an input artifact. It is not a Support Signal, not a Support Case, and not a Service Request.

See [CHECKINS.md](CHECKINS.md).

---

## Support Case

Coordination around a Veteran. A Support Case groups one or more Service Requests, notes, assignments, follow-ups, and a Settlement. States: `OPEN`, `TRIAGED`, `ASSIGNED`, `ACTIVE`, `FOLLOWUP`, `RESOLVED`, `CLOSED`. Closure does not delete history.

See [CASES.md](CASES.md).

---

## Service Request

A specific requested need (for example: a meal, a ride, a night of temporary shelter, a peer conversation). One Support Case may contain multiple Service Requests. States: `CREATED`, `SUBMITTED`, `TRIAGED`, `MATCHING`, `ASSIGNED`, `ACCEPTED`, `IN_PROGRESS`, `FULFILLED`, `CONFIRMED`, `CLOSED`, plus exception states `CANCELLED`, `DECLINED`, `EXPIRED`, `UNFULFILLABLE`, `ESCALATED`.

See [DISPATCH.md](DISPATCH.md).

---

## Referral

A directed handoff of a Veteran (with consent) to a destination Resource or Service Provider. Distinct from a Service Request. Sending a Referral is not fulfillment and is not evidence that a service was received.

See [REFERRALS.md](REFERRALS.md).

---

## Resource

A first-class catalog entry describing an available support offering: organization, service name, category, eligibility, counties, coverage, hours, contact method, referral method, cost, capacity, active flag, `last_verified_at`, verification source.

See [RESOURCES.md](RESOURCES.md).

---

## Service Provider

An organization or person capable of fulfilling a Service Request. May own Resources and Service Offers. Distinct from Responder (coordinator) unless a specific membership makes the same human both, which must be explicit.

---

## Fulfillment

The record that a Service Request was accepted, started, completed, and confirmed (or failed, partial, or cancelled). A Service Request is not fulfilled merely because it is assigned. Funding is separate from fulfillment.

See [FULFILLMENT.md](FULFILLMENT.md).

---

## Follow-Up

A first-class work item with a due date, responsible party, retry policy, completion, reschedule, overdue, and escalation. Not a Case Note.

See [FOLLOWUP.md](FOLLOWUP.md).

---

## Settlement

An explicit resolution record for a Support Case: what was requested, what occurred, what was fulfilled, what remains unresolved, who confirmed, when, and remaining Follow-Up. Not a clinical outcome.

See [SETTLEMENT.md](SETTLEMENT.md).

---

## Consent Grant

A first-class, explicit, revocable, timestamped, versioned, auditable, purpose-scoped permission. Consent is not a boolean. Examples: `can_receive` for `YELLOW`/`ORANGE`/`RED`; `can_view` for `support_signal` / `checkin_answers` / `current_requests` / `location`. Revocation stops future use. Historical audit is preserved separately.

See [CONSENT.md](CONSENT.md).

---

## Domain Event

An immutable business fact emitted when a domain transition occurs (for example `CASE_CREATED`, `CONSENT_REVOKED`). Envelope defined in [EVENT_MODEL.md](EVENT_MODEL.md).

---

## Audit Event

An immutable security/operations record of who did what, when, to which record. Distinct from Domain Event. Audit Events are never mutated or deleted as part of ordinary operations.

See [EVENT_MODEL.md](EVENT_MODEL.md) and [SECURITY.md](SECURITY.md).

---

## Pilot

A bounded operational trial. v0.1 pilot: approximately 25–50 veterans, Santa Clara County, California. See [PILOT.md](PILOT.md).

---

## Assignment

The binding of a Responder (or Service Provider) to a Support Case or Service Request. Assignment is not Fulfillment.

---

## Case Note

A timestamped note on a Support Case. Notes are not Follow-Ups, not Settlements, not state transitions, and not Contact Attempts.

MVP visibility (`INFERRED`; D-015): veterans cannot see full Case Notes. See [CASES.md](CASES.md) section 8. Do not invent a clinical chart.

---

## QuestionnaireVersion

An immutable published version of the Check-In questionnaire. Check-Ins always reference a specific `QuestionnaireVersion`.

---

## Compliance Register

The inventory in [COMPLIANCE.md](COMPLIANCE.md) of legal and regulatory regimes that might apply to SUAS, with epistemic status and operational controls. A register is **not** a claim of compliance. Counsel and D-006 own legal classification. The register does not make SUAS HIPAA-compliant, CCPA-compliant, TCPA-compliant, or anything-compliant.

---

## External API

A third-party service capability required to operate the MVP loop (for example SMS delivery, email delivery, auth challenge, application host, relational database). Inventoried in [APIS.md](APIS.md) as **capability IDs**, not vendor names. Distinct from the SUAS product API contract in [API.md](API.md).

---

## Capability Port

The domain-facing interface (for example `SmsPort`, `EmailPort`, `AuthPort`) that a module calls instead of a vendor SDK. Vendor SDKs live only in adapters. Domain tests use fakes. Do not import a vendor name into domain specs. See [APIS.md](APIS.md). LOCAL sink adapters (stdout / mailbox file / no real send) are a proposed Path 3 in [FRICTION.md](FRICTION.md); they do not close D-001–D-005.

---

## Consent Preset

A named bundle that **writes** first-class Consent Grant rows in one veteran action. A Consent Preset is not a boolean, not a blanket "I agree," and not a skip of purpose, template version, revoke, or evaluate-at-use. Proposed IDs (analysis in [FRICTION.md](FRICTION.md); not implementation authority): `PRESET_RED_ONLY` (`can_receive` + `RED`), `PRESET_ORANGE_RED` (`can_receive` + `ORANGE` and `can_receive` + `RED`, two rows), `PRESET_SHARE_REQUESTS` (`can_view` + `current_requests`; not `checkin_answers`; not `location`).

---

## Help-first request

A veteran first-run path whose primary call-to-action is an explicit Service Request (`FOOD` / `TRANSPORTATION` / `SHELTER` / `PEER_SUPPORT`). Check-In is optional after, not a first-run blocker. NEED from an explicit request is already allowed ([ONBOARDING.md](ONBOARDING.md), [CHECKINS.md](CHECKINS.md)). Specified as a proposed path in [FRICTION.md](FRICTION.md); not implementation authority.

---

## First-run / Bootstrap Checklist

The gated, persisted, auditable sequence that makes an empty environment operable ([ONBOARDING.md](ONBOARDING.md)). Runs once per environment (`LOCAL` / `TEST` / `STAGING` / `PRODUCTION`). Closing it emits Audit Events. Distinct from Pilot enrollment and from Trusted Circle membership.

---

## Contact Attempt

A first-class log of a Responder contact with a Veteran on a Support Case. Created by `POST /cases/{id}/commands/log-contact-attempt` and `POST /cases/{id}/commands/complete-contact`. Required fields: `at`, `channel`, `outcome`, `actor_id`. Emits `RESPONDER_CONTACT_LOGGED`. A Case Note is not a substitute.

See [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [API.md](API.md).

---

## Forbidden aliases

Do not use these as contract names (API resources, state names, glossary substitutes, or UI labels that leak into contracts). See [SPEC-001.md](SPEC-001.md) and [PRODUCT.md](PRODUCT.md) section 6.

| Forbidden | Use instead |
|---|---|
| ticket | Support Case |
| alert (as the signal) | Support Signal |
| referral (as the request) | Service Request |
| case (as the request) | Service Request |
| HIPAA-compliant / HIPAA compliant | `HIPAA_APPLICABILITY = DECISION_PENDING` |
| AI-powered, smart matching, seamless, intelligent, automatically handles | omit; specify exact behavior |

"Trusted-contact alert" is a named notify action, not an alias of Support Signal.

---

## Epistemic labels used in this stack

`OBSERVED`, `INFERRED`, `SPECULATIVE`, `NOT_COMPUTABLE`, `DECISION_PENDING`, `FUTURE`. Defined in [README.md](README.md#11-epistemic-labels).
