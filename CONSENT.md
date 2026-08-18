# CONSENT.md — First-class Consent Grants (SUAS v0.1)

**Related:** [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [SAFETY.md](SAFETY.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [PRIVACY.md](PRIVACY.md), [EVENT_MODEL.md](EVENT_MODEL.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [FRICTION.md](FRICTION.md)

**Actors:** Veteran (grantor), Trusted Contact / Responder / Organization / Service Provider / System (grantees), SUAS System Administrator (audit only).

---

## 1. Purpose

Consent is a first-class domain object, **not a boolean**. Every disclosure, notification, trusted-contact alert, provider-side fulfillment disclosure, and out-of-scope assignment requires a matching **Consent Grant** or an explicitly documented in-product system basis evaluated at use time.

---

## 2. Grant shape

A Consent Grant has:

| Field | Rule |
|---|---|
| `permission` | Explicit verb, e.g. `can_receive`, `can_view`, `can_share` |
| `scope` | Explicit object, e.g. `YELLOW`, `ORANGE`, `RED`, `support_signal`, `checkin_answers`, `current_requests`, `location`, `service_request_fulfillment` |
| `purpose` | Why the grant exists (text bound to a `consent_template_version`) |
| `grantee_type` / `grantee_id` | Who receives the permission |
| `consent_template_version` | Template the veteran accepted |
| `granted_at` | Timestamp |
| `status` | `ACTIVE` / `REVOKED` / `EXPIRED` |
| `expires_at` | Optional |

Grants are **revocable, timestamped, versioned, auditable, purpose-scoped**.

### 2.1 Required grant examples (MVP)

- `can_receive` + `YELLOW` | `ORANGE` | `RED` — whether a Trusted Contact (or other grantee) may be notified at that Support Signal level.
- `can_view` + `support_signal` | `checkin_answers` | `current_requests` | `location`.
- `can_share` + `service_request_fulfillment` — permits minimum-necessary Service Request data to be disclosed to a named/selected Service Provider for a fulfillment attempt. Location or other sensitive scopes still require explicit coverage when transmitted.

A grant for `YELLOW` does not imply `ORANGE` or `RED`. A grant for `support_signal` does not imply `checkin_answers`. A grant to fulfill a Service Request does not authorize unrelated Case Notes, Check-In answers, Support Signal basis, or Trusted Circle data.

---

## 3. Evaluation rules

1. Evaluate at the moment of use. Do not cache "visible forever."
2. Missing grant = deny unless a specific accepted/released system basis authorizes the action without third-party disclosure.
3. `REVOKED` or `EXPIRED` = deny for future use.
4. Purpose must match the action (notify vs view vs refer vs provider fulfillment).
5. System actions that do not disclose to a third party (compute a signal, write an audit row) do not require a third-party grant. They still require the veteran to be enrolled and the action to be in-product-scope.
6. Responder case-assignment access is **not** a Trusted Contact grant. It is a separate, documented, least-privilege policy: an assigned Responder may view fields listed in [CASES.md](CASES.md) and [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md) for an active assignment. That policy must still be recorded as a system-purpose grant or an equivalent documented basis on each access Audit Event (`consent_basis`).
7. Sending a Referral requires an `ACTIVE` grant covering the destination and the data included. See [REFERRALS.md](REFERRALS.md).
8. Starting or advancing an external provider Fulfillment Attempt is a third-party disclosure when veteran/request data crosses the SUAS boundary. Before the adapter call, the system must evaluate an `ACTIVE` grant/system basis covering the provider, purpose, and exact data projection.
9. Provider adapters receive only the minimum fields needed for that capability. The adapter must not receive entire Support Case, Check-In, Case Note, Trusted Circle, or audit payloads by default.
10. Consent evaluation must occur **before each external mutation that newly discloses data**. A previous quote/search/attempt does not create permanent permission for later booking, reroute, retry, or replacement-provider disclosure.
11. If a provider is replaced or rerouted, consent must cover the new grantee/provider and projection before disclosure.

---

## 4. Revocation

- Veteran may revoke any grant they issued.
- Revocation sets `status=REVOKED`, `revoked_at=now`, writes a `ConsentEvent`, emits `CONSENT_REVOKED`.
- **Revocation stops future use** immediately: in-flight notification jobs and provider jobs must re-check before any not-yet-sent disclosure or external mutation; queued work whose grant is revoked is cancelled or moved to a non-disclosing/manual review state and recorded.
- Revocation does not imply that a provider can erase data already lawfully sent. Any provider-side deletion/retention obligation is governed by the selected provider agreement and applicable law; do not invent it here.
- **Historical audit is preserved separately** on `ConsentEvent` and `AuditEvent`. Do not delete the grant row's history. Do not reuse the revoked row as a new grant; insert a new grant if the veteran re-consents.

---

## 5. Provider disclosure projection

For each provider capability, [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md) defines a SUAS-owned request context/projection. The projection must be auditable.

At minimum, an external provider disclosure Audit Event records:

- `service_request_id`;
- `fulfillment_attempt_id` when applicable;
- grantee/provider adapter identity;
- `consent_basis`;
- purpose/capability;
- field names/categories disclosed (not secret/body dumps);
- timestamp and actor/system job identity.

Provider SDK request objects are not consent records. Provider terms or SDK defaults do not expand SUAS consent scope.

---

## 6. Templates

Grants reference `consent_template_version`. Template text is published by SUAS-admin ([ADMIN.md](ADMIN.md)). Exact template copy is `NOT_COMPUTABLE` until written. Do not ship grants against unpublished templates.

---

## 7. States

`ACTIVE` → `REVOKED` | `EXPIRED`

No hidden states. A denied request that never created a grant still writes a `ConsentEvent` of type `DENIED` when a user attempts an action that requires consent and is refused.

---

## 8. Events

- Domain: `CONSENT_GRANTED`, `CONSENT_REVOKED`
- ConsentEvent: `GRANTED`, `REVOKED`, `EXPIRED`, `DENIED`, `TEMPLATE_ACCEPTED`
- Audit: every evaluate-for-disclosure that returns allow or deny on third-party data; every actual provider disclosure attempt/result references the consent basis

---

## 9. Non-goals

- Blanket "I agree" that unlocks all scopes
- Implied consent from Trusted Circle membership
- Implied consent from downloading the PWA
- Using preferences ([NOTIFICATIONS.md](NOTIFICATIONS.md)) as consent
- Treating provider selection or terms-of-service acceptance as SUAS Consent Grant replacement
- Sending entire case/check-in payloads because a provider SDK makes that convenient
- Legal interpretation of HIPAA authorization (`HIPAA_APPLICABILITY = DECISION_PENDING`)

---

## 10. Testability

Critical suite: **consent revocation** ([TESTING.md](TESTING.md)).

- Grant allows notify; revoke; subsequent notify must not send.
- `can_view support_signal` does not reveal `checkin_answers`.
- `can_receive YELLOW` does not allow `RED` alert.
- Historical ConsentEvent remains after revoke.
- Referral send without grant fails.
- Provider fulfillment call without matching grant/system basis fails before adapter invocation.
- Provider projection excludes Case Notes, Check-In answers, Support Signal basis, and Trusted Circle data unless separately and explicitly authorized.
- Revoke before queued provider mutation prevents that mutation from disclosing new data.
- Reroute to a different provider re-evaluates consent for the new grantee.
