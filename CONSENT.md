# CONSENT.md — First-class Consent Grants (SUAS v0.1)

**Related:** [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [SAFETY.md](SAFETY.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [PRIVACY.md](PRIVACY.md), [EVENT_MODEL.md](EVENT_MODEL.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [FRICTION.md](FRICTION.md)

**Actors:** Veteran (grantor), Trusted Contact / Responder / Organization / Service Provider / System (grantees), SUAS System Administrator (audit only).

---

## 1. Purpose

Consent is a first-class domain object, **not a boolean**. Every disclosure, notification, trusted-contact alert, and out-of-scope assignment requires a matching **Consent Grant** evaluated at use time.

---

## 2. Grant shape

A Consent Grant has:

| Field | Rule |
|---|---|
| `permission` | Explicit verb, e.g. `can_receive`, `can_view` |
| `scope` | Explicit object, e.g. `YELLOW`, `ORANGE`, `RED`, `support_signal`, `checkin_answers`, `current_requests`, `location` |
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

A grant for `YELLOW` does not imply `ORANGE` or `RED`. A grant for `support_signal` does not imply `checkin_answers`.

---

## 3. Evaluation rules

1. Evaluate at the moment of use. Do not cache "visible forever."
2. Missing grant = deny.
3. `REVOKED` or `EXPIRED` = deny for future use.
4. Purpose must match the action (notify vs view vs refer).
5. System actions that do not disclose to a third party (compute a signal, write an audit row) do not require a third-party grant. They still require the veteran to be enrolled and the action to be in-product-scope.
6. Responder case-assignment access is **not** a Trusted Contact grant. It is a separate, documented, least-privilege policy: an assigned Responder may view fields listed in [CASES.md](CASES.md) and [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md) for an active assignment. That policy must still be recorded as a system-purpose grant or an equivalent documented basis on each access Audit Event (`consent_basis`).
7. Sending a Referral requires an `ACTIVE` grant covering the destination and the data included. See [REFERRALS.md](REFERRALS.md).

---

## 4. Revocation

- Veteran may revoke any grant they issued.
- Revocation sets `status=REVOKED`, `revoked_at=now`, writes a `ConsentEvent`, emits `CONSENT_REVOKED`.
- **Revocation stops future use** immediately: in-flight notification jobs must re-check before send; queued notifications whose grant is revoked are cancelled and recorded.
- **Historical audit is preserved separately** on `ConsentEvent` and `AuditEvent`. Do not delete the grant row's history. Do not reuse the revoked row as a new grant; insert a new grant if the veteran re-consents.

---

## 5. Templates

Grants reference `consent_template_version`. Template text is published by SUAS-admin ([ADMIN.md](ADMIN.md)). Exact template copy is `NOT_COMPUTABLE` until written. Do not ship grants against unpublished templates.

---

## 6. States

`ACTIVE` → `REVOKED` | `EXPIRED`

No hidden states. A denied request that never created a grant still writes a `ConsentEvent` of type `DENIED` when a user attempts an action that requires consent and is refused.

---

## 7. Events

- Domain: `CONSENT_GRANTED`, `CONSENT_REVOKED`
- ConsentEvent: `GRANTED`, `REVOKED`, `EXPIRED`, `DENIED`, `TEMPLATE_ACCEPTED`
- Audit: every evaluate-for-disclosure that returns allow or deny on third-party data

---

## 8. Non-goals

- Blanket "I agree" that unlocks all scopes
- Implied consent from Trusted Circle membership
- Implied consent from downloading the PWA
- Using preferences ([NOTIFICATIONS.md](NOTIFICATIONS.md)) as consent
- Legal interpretation of HIPAA authorization (`HIPAA_APPLICABILITY = DECISION_PENDING`)

---

## 9. Testability

Critical suite: **consent revocation** ([TESTING.md](TESTING.md)).

- Grant allows notify; revoke; subsequent notify must not send.
- `can_view support_signal` does not reveal `checkin_answers`.
- `can_receive YELLOW` does not allow `RED` alert.
- Historical ConsentEvent remains after revoke.
- Referral send without grant fails.
