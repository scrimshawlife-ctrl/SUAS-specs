# FRICTION.md — User and infra friction, and how to engineer through it (SUAS v0.1)

**Status:** `draft` / `0.1.0`. Analysis + proposed engineering paths. **Not** implementation authority. **Not** a decision close.  
**SPEC-001 status:** `READY_FOR_REVIEW` (not `accepted`; not `released`; see [SPEC-001.md](SPEC-001.md))  
**Related:** [ONBOARDING.md](ONBOARDING.md), [CONSENT.md](CONSENT.md), [SAFETY.md](SAFETY.md), [APIS.md](APIS.md), [DEPLOYMENT.md](DEPLOYMENT.md), [AUTH.md](AUTH.md), [DECISIONS.md](DECISIONS.md), [SPEC-001.md](SPEC-001.md), [ARCHITECTURE.md](ARCHITECTURE.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [RESOURCES.md](RESOURCES.md), [CHECKINS.md](CHECKINS.md)

This file names friction and proposed paths. It does not accept or release any specification. It does not close D-001–D-016. It does not invent partners, vendors, HIPAA status, signal weights, or local crisis-resource lists.

---

## 1. Thesis

The v0.1 stack is internally consistent. The cost is a **correctness tax** on a 25–50 veteran Santa Clara County pilot.

Correctness here means the standing rules already specified: first-class Consent Grants, no automated 911, no generative primary Support Signal, no HIPAA claim, no invented partners. Those rules stay.

This file names **user friction** and **infra friction**, then three engineering paths that reduce that tax **without** weakening those rules.

Paths are `SPECULATIVE` until an owner records a decision or a later spec version accepts them. Draft analysis is not a contract to implement.

---

## 2. User friction (by role)

### 2.1 Veteran

| Friction | Why it is a tax | Already specified (do not reopen) |
|---|---|---|
| Raw grant matrix | Veteran must assemble `can_receive` / `can_view` scopes by hand. Easy to under-grant or over-grant. | Consent is first-class grants, not a boolean ([CONSENT.md](CONSENT.md)). |
| First-run Check-In before help | First-run currently walks Check-In after enrollment even though NEED-from-explicit-request is already allowed. Help waits on a questionnaire the veteran can abandon. | Explicit Service Request without a completed Check-In is allowed ([ONBOARDING.md](ONBOARDING.md) §7.1, [CHECKINS.md](CHECKINS.md), [APIS.md](APIS.md) §2.2). |
| Phone OTP offered when SMS is `UNAVAILABLE` | UI can present a method that cannot deliver. | Phone OTP depends on D-003; if SMS is `UNAVAILABLE`, do not offer phone OTP as if it worked ([AUTH.md](AUTH.md), [ONBOARDING.md](ONBOARDING.md) §7.1). |
| Trusted Circle treated as required | Optional invites are easy to present as a gate. | Trusted Circle is skippable; membership grants no visibility ([ONBOARDING.md](ONBOARDING.md) §3.1, [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md)). |
| Red-state blank if D-012 unset | Veteran sees no approved crisis copy; admin sees a blocked/incomplete banner. Correct, but empty. | Do not invent crisis-resource wording. D-012 remains `DECISION_PENDING` ([SAFETY.md](SAFETY.md), [DECISIONS.md](DECISIONS.md)). |

### 2.2 Responder

| Friction | Why it is a tax | Already specified (do not reopen) |
|---|---|---|
| Assignment buried as fulfillment | Ops shortcut: assigned looks "done." | Assignment is not Fulfillment ([DISPATCH.md](DISPATCH.md), [FULFILLMENT.md](FULFILLMENT.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md)). |
| Contact log buried as a Case Note | Notes are easier to type; the contact log is the required record. | `log-contact-attempt` / `complete-contact`; a Case Note is not a substitute ([API.md](API.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md)). |
| Console can feel like an EHR | Clinical-chart gravity. | Coordination console, not an EHR ([PRODUCT.md](PRODUCT.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md)). |
| Empty queue looks broken | Zero cases is valid; without next actions the console feels unfinished. | Empty queue is valid; show next actions ([ONBOARDING.md](ONBOARDING.md) §6). |

### 2.3 Admin (SUAS System Administrator / Organization Administrator)

| Friction | Why it is a tax | Already specified (do not reopen) |
|---|---|---|
| 11-step bootstrap | Empty system cannot enroll until gates close. | Gated, persisted, auditable first-run ([ONBOARDING.md](ONBOARDING.md) §4). |
| D-012 acknowledgment vs invented copy | Admin must record "unset" rather than write unofficial crisis text. | Acknowledgment is the gate; invented veteran-facing copy is forbidden ([ONBOARDING.md](ONBOARDING.md) §4.3 step 9). |
| Questionnaire + signal publish before enroll | Veterans cannot enroll without a published `QuestionnaireVersion`; signal rules may be a labeled fixture only. | Hard gates ([ONBOARDING.md](ONBOARDING.md) §4.3 steps 5–6). D-011 stays open. |
| Four separate environment bootstraps | `LOCAL` / `TEST` / `STAGING` / `PRODUCTION` do not copy checklist state. | Once per environment ([ONBOARDING.md](ONBOARDING.md) §2, [DEPLOYMENT.md](DEPLOYMENT.md)). |

Org-admin cannot publish global questionnaire or signal rules. That isolation is not friction to remove.

### 2.4 Trusted Contact

First-run **must** display actual grants (`can_receive` / `can_view` and their scopes), not "you are in the circle."

**Keep this.** Seeing actual grants is not friction to remove ([ONBOARDING.md](ONBOARDING.md) §7.3, [CONSENT.md](CONSENT.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md)).

### 2.5 Service Provider

No consumer app in MVP. First-run is acceptance of an assignment (`ACCEPTED` on the Service Request / fulfillment path).

**Keep this.** Do not build a Service Provider portal for MVP ([ONBOARDING.md](ONBOARDING.md) §7.4, [FULFILLMENT.md](FULFILLMENT.md)).

---

## 3. Infra friction

| Friction | Why it is a tax | Already specified (do not reopen) |
|---|---|---|
| D-001–D-005 open | Waiting for hosting / auth / SMS / email / DB-hosting vendors blocks a **real** STAGING/PRODUCTION deploy. | Vendors remain `DECISION_PENDING`. Do not lock a vendor ([DECISIONS.md](DECISIONS.md), [APIS.md](APIS.md), [DEPLOYMENT.md](DEPLOYMENT.md)). |
| Four isolated environments | Separate databases, secrets, notification credentials, telemetry, user data. | Required ([DEPLOYMENT.md](DEPLOYMENT.md) §2). |
| SMS/email `UNAVAILABLE` vs fake-send | Channels without a provider must be marked `UNAVAILABLE`. Fake-send to real people is forbidden. | [ONBOARDING.md](ONBOARDING.md) §4.1 step 8, [APIS.md](APIS.md) §3.1, [NOTIFICATIONS.md](NOTIFICATIONS.md). |
| `GEOCODE_MAPS` (D-014) | `coverage_geometry` vs a county list. A maps client is not specified as required. | D-014 `DECISION_PENDING` ([RESOURCES.md](RESOURCES.md), [APIS.md](APIS.md) §3.2). |
| D-013 counsel before pilot ops | Counsel review of [COMPLIANCE.md](COMPLIANCE.md) is a pilot-operation gate. | Remains open. Not a compliance claim. |
| Immutable events + no child notification table | One Notification row; retries append immutable Audit Events. | Specified ([NOTIFICATIONS.md](NOTIFICATIONS.md), [EVENT_MODEL.md](EVENT_MODEL.md)). |
| Modular monolith | Correct for this pilot. Microservices would add network and deploy friction without a demonstrated need. | Modular monolith ([ARCHITECTURE.md](ARCHITECTURE.md)). Do not extract services. |

D-001–D-005 block a **production-shaped** deploy if we wait. They do not have to block a `LOCAL` loop if Path 3 adapters exist. That is not a vendor close.

---

## 4. Path 1 — Consent presets + help-first veteran path

**Status of this path:** proposed engineering. Not implementation authority. Does not weaken [CONSENT.md](CONSENT.md).

### 4.1 Help-first

- Veteran primary CTA after enrollment: an **explicit** Service Request in an MVP category (`FOOD` / `TRANSPORTATION` / `SHELTER` / `PEER_SUPPORT`).
- Check-In is **optional after**, not a first-run blocker.
- NEED-from-explicit-request is already allowed. This path makes that the default first action instead of burying it after Check-In.
- Trusted Circle stays **optional** and **after first help** if the veteran wants it. Do not dark-pattern invites.

### 4.2 Hide undeliverable phone OTP

When the SMS channel is `UNAVAILABLE` in that environment, **do not offer** phone OTP. Email magic-link / email OTP remain the passwordless path ([AUTH.md](AUTH.md)).

### 4.3 Named consent presets

Presets **write** first-class Consent Grant rows. A preset is not a boolean and not a blanket "I agree."

| Preset ID | Grant rows written | Does not write |
|---|---|---|
| `PRESET_RED_ONLY` | `can_receive` + `RED` | `ORANGE`, `YELLOW`, `checkin_answers`, `location` |
| `PRESET_ORANGE_RED` | `can_receive` + `ORANGE`; `can_receive` + `RED` (two rows) | `YELLOW`, `checkin_answers`, `location` |
| `PRESET_SHARE_REQUESTS` | `can_view` + `current_requests` | `checkin_answers`, `location`, signal-level notify grants |

Rules:

1. Each row is a Consent Grant as specified in [CONSENT.md](CONSENT.md): `permission`, `scope`, `purpose`, `grantee_type` / `grantee_id`, `consent_template_version`, `granted_at`, `status`.
2. A preset does **not** skip purpose, template version, revoke, or evaluate-at-use.
3. A grant for `ORANGE` still does not imply `RED`. `PRESET_ORANGE_RED` writes **both** rows.
4. Veteran may later revoke **individual** grants. Revoking one row does not silently revoke the others.
5. Missing grant = deny. Evaluate at use.

### 4.4 Template copy

Consent template copy remains unpublished until written (`NOT_COMPUTABLE`). Preset **IDs** may exist in this analysis. User-facing sentences must not be invented as official crisis copy. D-012 stays open except for the **proposed** narrow close in §8 (not accepted here).

---

## 5. Path 2 — Fixture-first LOCAL bootstrap + narrow D-012

**Status of this path:** proposed engineering. Not implementation authority. Does not close D-011, D-012, D-013, or D-014 in [DECISIONS.md](DECISIONS.md).

### 5.1 Bootstrap without invented partners

- SUAS-admin may act as first org-admin for `PARTNER_ORG_001` (already allowed; [ONBOARDING.md](ONBOARDING.md) §4.1 step 4).
- Present the 11 steps as a **wizard**. Hard gates remain. Soft-parallel draft is already allowed (e.g. draft resources while a questionnaire is `DRAFT`). Publish / close still requires the gates.
- Publish a labeled `UNRELEASED_FIXTURE` `QuestionnaireVersion` and a labeled `UNRELEASED_FIXTURE` signal-rule version. D-011 remains open. Fixtures are **not** shipped thresholds and not production `signal_version` policy ([SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [ONBOARDING.md](ONBOARDING.md) §4.1 step 6).
- County list only for Santa Clara County. No maps client in MVP.

### 5.2 Proposed owner close of D-014 (not accepted in this pass)

**Proposal:** `GEOCODE_MAPS` is **not required** for MVP. A county list suffices for the Santa Clara pilot. `coverage_geometry` stays optional.

Do **not** close D-014 in [DECISIONS.md](DECISIONS.md) in this pass. Owner must record it.

### 5.3 Proposed narrow owner close of D-012 (not accepted in this pass)

**Proposal (narrow):** display only national public numbers — **911** and **988** — as `OBSERVED` public emergency / crisis lines, with the sentence: "SUAS does not call them for you."

- No local Santa Clara County resource names.
- No invented partner hotlines.
- Local resources stay D-008 / D-012 remainder.
- This is display, not dispatch. SUAS still does not auto-dial, auto-SMS public safety, or create an emergency ticket ([SAFETY.md](SAFETY.md)).

Until the owner actually records this close, red-state behavior is unchanged: admin banner / no invented copy to veterans.

### 5.4 Fixtures and channels

- `TEST` / `STAGING` fixture veterans are labeled (`UNRELEASED_FIXTURE` or equivalent). Never promote to `PRODUCTION` ([ONBOARDING.md](ONBOARDING.md) §6, [DEPLOYMENT.md](DEPLOYMENT.md)).
- Email / SMS remain `UNAVAILABLE` in `LOCAL` unless a **sink adapter** is configured (Path 3). A sink is not a vendor close and is not a send to a real person.

---

## 6. Path 3 — Capability-port LOCAL adapters

**Status of this path:** proposed first engineering slice **after** a `released` cut. This file does not authorize that work now.

### 6.1 Port rule (already specified)

Domain modules call `AuthPort`, `SmsPort`, `EmailPort` only. Vendor SDKs live only in adapters. Domain tests use fakes ([APIS.md](APIS.md) §4, [GLOSSARY.md](GLOSSARY.md) Capability Port).

### 6.2 LOCAL adapters (proposed)

| Port | LOCAL adapter behavior |
|---|---|
| `AuthPort` | Magic-link / OTP printed to stdout or a local mailbox file. Not a production auth vendor. |
| `SmsPort` | Sink. No real send. Channel may be marked available **to the sink only**. |
| `EmailPort` | Sink. No real send. Same rule. |
| `RELATIONAL_DB` | PostgreSQL on the developer machine. This is not a close of D-005 (database **hosting**). |
| `APP_HOST` | The developer machine. This is not a close of D-001 (production hosting). |

### 6.3 What this does not do

- Does **not** close D-001–D-005. It lets the canonical loop run in `LOCAL` without pretending vendors are chosen.
- `STAGING` / `PRODUCTION` still require real providers and D-013 before pilot operations.
- No 911 / PSAP, VA, Medi-Cal / X12, FHIR-write, payment-card, social-login, address-book, or continuous-GPS clients ([APIS.md](APIS.md) §3.3).
- Sinks must not contain a real veteran address. A sink that messages a real person is a spec violation, same as fake-send.

---

## 7. What we will not weaken

Standing non-goals remain ([PRODUCT.md](PRODUCT.md) §8, [SAFETY.md](SAFETY.md) §2). Paths 1–3 must not weaken:

1. Consent is first-class grants, not a boolean. Presets **write grant rows**.
2. No automated emergency dispatch (including automated 911).
3. No generative model for the primary Support Signal or any safety-critical decision.
4. No HIPAA claim. `HIPAA_APPLICABILITY = DECISION_PENDING`.
5. No invented partners. Placeholders stay `PARTNER_ORG_001` … until D-008.
6. No invented signal weights. D-011 stays open. Fixtures stay labeled `UNRELEASED_FIXTURE`.
7. No invented local crisis-resource lists. D-012 remainder (local copy) stays open.
8. Assignment is not Fulfillment. Contact log is not a Case Note. Follow-Up is not a Case Note. Settlement is not a clinical outcome.
9. Trusted Circle membership grants no visibility.
10. Sinks do **not** fake-send to real people.
11. No production data in non-prod. No fixture veterans promoted to `PRODUCTION`.
12. Modular monolith for the pilot. No microservices extraction.
13. No 911 / VA / Medi-Cal / FHIR / card / social / GPS clients.

---

## 8. Proposed decision closes (owner must accept; still open)

These are **proposals**. They are not closes. [DECISIONS.md](DECISIONS.md) status is unchanged. Agents must not self-accept.

| ID | Proposal | Not a close until |
|---|---|---|
| D-014 | `GEOCODE_MAPS` not required for MVP. County list only; no maps client. | Owner records it in [DECISIONS.md](DECISIONS.md). |
| D-012 (narrow) | 911 + 988 display-only, as `OBSERVED` public lines, with "SUAS does not call them for you." | Owner records it in [DECISIONS.md](DECISIONS.md). Remainder of D-012 (local copy) stays open even after a narrow close. |

D-001 through D-011, D-013, D-015, and D-016 are not proposed for close.

---

## 9. Implementation

Implementation authority remains `NOT_YET_RELEASED` ([STATUS.md](STATUS.md)).

This file does **not** authorize `SUAS` feature work. Draft analysis is not a released contract.

After a `released` cut of the relevant specs, **Path 3 LOCAL adapters** are the first engineering slice: ports + sinks + local Postgres, so the loop can run on a developer machine without vendor decisions.

Path 1 (presets + help-first) and Path 2 (fixture-first wizard + owner closes) follow only if the owner accepts the related proposals and the specs that name them are `accepted` then `released`.

---

## 10. Testability

These checks apply **if and when** a path is implemented against a released spec. They are not a license to implement now.

| Check | Pass condition |
|---|---|
| Preset writes N grant rows | `PRESET_ORANGE_RED` inserts two Consent Grant rows, not one boolean. `PRESET_RED_ONLY` inserts one `can_receive`+`RED` row. `PRESET_SHARE_REQUESTS` inserts `can_view`+`current_requests` and does not insert `checkin_answers` or `location`. |
| Evaluate-at-use | Preset-written grants are re-checked at notify/view; revoke of one row denies that scope only. |
| Help-first | Veteran can create a Service Request without a completed Check-In. Check-In is not a first-run hard gate. |
| Phone OTP hidden | When SMS is `UNAVAILABLE`, the auth UI does not offer phone OTP. |
| LOCAL sink | Sink adapter never holds a real veteran address; no real SMS/email send. |
| Fixture label | QuestionnaireVersion and signal-rule fixture rows are marked `UNRELEASED_FIXTURE`. Fixture veterans in TEST/STAGING are labeled and cannot be promoted to PRODUCTION. |
| No maps client | MVP has no geocoding/maps client unless D-014 is closed the other way (maps required) and recorded in [DECISIONS.md](DECISIONS.md). |
| Red-state copy | Shows 911/988 display-only **only after** the narrow D-012 close is actually recorded. Until then: admin banner / no invented copy to veterans. |
| No vendor close | LOCAL adapters do not write a vendor name into [DECISIONS.md](DECISIONS.md) or domain modules. |
| Forbidden clients | No 911/PSAP, VA, Medi-Cal/X12, FHIR-write, payment-card, social-login, address-book, or continuous-GPS clients. |

---

## 11. Non-goals of this file

- Closing D-001–D-016
- Accepting or releasing any specification
- Authorizing `SUAS` feature work
- Inventing partners, vendors, HIPAA status, signal weights, or local crisis-resource lists
- Building a Service Provider portal
- Removing Trusted Contact grant display
- Treating a sink as a production provider
- Treating an `UNRELEASED_FIXTURE` as shipped policy
- Extracting microservices

---

## 12. Links

[ONBOARDING.md](ONBOARDING.md), [CONSENT.md](CONSENT.md), [SAFETY.md](SAFETY.md), [APIS.md](APIS.md), [DEPLOYMENT.md](DEPLOYMENT.md), [AUTH.md](AUTH.md), [DECISIONS.md](DECISIONS.md), [SPEC-001.md](SPEC-001.md), [ARCHITECTURE.md](ARCHITECTURE.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [RESOURCES.md](RESOURCES.md), [CHECKINS.md](CHECKINS.md), [PRODUCT.md](PRODUCT.md), [STATUS.md](STATUS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [GLOSSARY.md](GLOSSARY.md)
