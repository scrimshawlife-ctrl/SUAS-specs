# EVENT_MODEL.md — Domain Events and Audit Events (SUAS v0.1)

**Related:** [DATA_MODEL.md](DATA_MODEL.md), [ARCHITECTURE.md](ARCHITECTURE.md), [VERSIONING.md](VERSIONING.md), [SECURITY.md](SECURITY.md)

---

## 1. Purpose

Record immutable business facts (Domain Events) and immutable who/what/when security/operations facts (Audit Events).

Audit Events are **immutable**. Application roles cannot update or delete them.

---

## 2. Envelope (both stores)

| Field | Required | Notes |
|---|---|---|
| `event_id` | yes | UUID, idempotency key |
| `event_type` | yes | From the catalog below for domain events |
| `aggregate_type` | yes | e.g. `SupportCase`, `ServiceRequest` |
| `aggregate_id` | yes | |
| `tenant_id` | yes | |
| `actor_type` | yes | `VETERAN` / `RESPONDER` / `ORG_ADMIN` / `SUAS_ADMIN` / `TRUSTED_CONTACT` / `SERVICE_PROVIDER` / `SYSTEM` |
| `actor_id` | yes | User id or `system` |
| `occurred_at` | yes | Server time |
| `schema_version` | yes | Event schema version; stack starts `0.1.0` |
| `payload` | yes | Structured; no secrets; minimize sensitive free-text |

---

## 3. Domain event catalog

Exactly these types are defined for v0.1. Adding a type is a MINOR spec change.

| event_type | Typical aggregate |
|---|---|
| `VETERAN_ENROLLED` | VeteranProfile / PilotEnrollment |
| `CHECKIN_COMPLETED` | CheckIn |
| `SUPPORT_SIGNAL_CHANGED` | SupportSignal |
| `CASE_CREATED` | SupportCase |
| `CASE_ASSIGNED` | SupportCase / CaseAssignment |
| `RESPONDER_CONTACT_LOGGED` | SupportCase |
| `CASE_ESCALATED` | SupportCase |
| `CASE_RESOLVED` | SupportCase |
| `SERVICE_REQUEST_CREATED` | ServiceRequest |
| `SERVICE_REQUEST_ASSIGNED` | ServiceRequest |
| `SERVICE_ACCEPTED` | ServiceRequest / ServiceFulfillment |
| `SERVICE_FULFILLED` | ServiceRequest / ServiceFulfillment |
| `SERVICE_FAILED` | ServiceRequest / ServiceFulfillment |
| `REFERRAL_CREATED` | Referral |
| `REFERRAL_UPDATED` | Referral |
| `FOLLOWUP_CREATED` | FollowUp |
| `FOLLOWUP_DUE` | FollowUp |
| `FOLLOWUP_COMPLETED` | FollowUp |
| `TRUSTED_CONTACT_INVITED` | TrustedContact |
| `CONSENT_GRANTED` | ConsentGrant |
| `CONSENT_REVOKED` | ConsentGrant |
| `TRUSTED_CONTACT_ALERTED` | TrustedContact |

---

## 4. Audit Events

Audit Events use the same envelope plus `action`, `target_type`, `target_id`, and optional request metadata (`ip`, `user_agent`). They cover:

- auth success/fail, session invalidate, recovery
- admin writes
- consent evaluate allow/deny for third-party disclosure
- exports
- notification send attempts
- every domain state transition (may reference `event_id` of the Domain Event)

---

## 5. Immutability and idempotency

- Insert only. Unique `event_id`.
- Producers retry with the same `event_id`.
- No application UPDATE/DELETE.
- Retention of event stores: D-007 `DECISION_PENDING`. Do not invent a purge job that destroys audit.

Critical suite: **audit-event immutability** ([TESTING.md](TESTING.md)).

---

## 6. Notifications

Events and policies generate notifications ([NOTIFICATIONS.md](NOTIFICATIONS.md)). Emitting an event is not itself a send.

---

## 7. Non-goals

- Event-sourcing as the only system of record ([ARCHITECTURE.md](ARCHITECTURE.md))
- PII-rich payloads (put identifiers + codes; keep free-text out unless necessary)
