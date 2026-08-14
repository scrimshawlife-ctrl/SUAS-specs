# REFERRALS.md — Referrals (distinct from Service Requests)

**Related:** [DISPATCH.md](DISPATCH.md), [RESOURCES.md](RESOURCES.md), [CONSENT.md](CONSENT.md), [FOLLOWUP.md](FOLLOWUP.md), [FULFILLMENT.md](FULFILLMENT.md)

**Actors:** Responder, destination Organization / Service Provider, Veteran, System.

---

## 1. Purpose

A **Referral** is a directed, consented handoff to a destination Resource or Service Provider. It is **not** a Service Request. Sending a Referral is **not** Fulfillment and is **not** evidence that a service was received.

---

## 2. Required content

- Consent: `consent_grant_id` that is `ACTIVE` at send time
- Destination: `destination_type` + `destination_id`
- Reason
- Method (how it was sent: in-app, phone, email — recorded, not invented integrations)
- Status
- Result (when known)
- Follow-up (link a Follow-Up when a check-back is required)

Optional: `service_request_id` if the referral is in service of a request. The request does not auto-transition to `FULFILLED` because a referral was sent.

---

## 3. States

`DRAFTED` → `SENT` → `ACKNOWLEDGED` → `ACCEPTED` | `DECLINED` → `COMPLETED` | `UNABLE_TO_SERVE` | `CANCELLED`

| Transition | Actor | Notes |
|---|---|---|
| → `DRAFTED` | Responder | May exist without send |
| `DRAFTED` → `SENT` | Responder | Grant must be ACTIVE; emit `REFERRAL_CREATED` if first persist+send, else `REFERRAL_UPDATED` |
| `SENT` → `ACKNOWLEDGED` | Destination or Responder-on-behalf | |
| `ACKNOWLEDGED` → `ACCEPTED` / `DECLINED` | Destination or Responder-on-behalf | Reason on decline |
| `ACCEPTED` → `COMPLETED` / `UNABLE_TO_SERVE` | Destination or Responder | `COMPLETED` means the destination reported they served — still not a ServiceFulfillment unless a Service Request fulfillment is separately recorded |
| any non-terminal | `CANCELLED` | Veteran or Responder |

---

## 4. Consent

No send without an `ACTIVE` grant covering the destination and the payload. Revocation after send does not erase the referral record; it blocks further payload expansion and further notifications to new parties.

---

## 5. Events

`REFERRAL_CREATED`, `REFERRAL_UPDATED`. Follow-up linked: `FOLLOWUP_CREATED`.

---

## 6. Non-goals

- Equating send with received service
- Silent fax/email integrations with unnamed vendors
- Sharing check-in answers with the destination unless a `can_view`/`purpose` grant says so

---

## 7. Testability

- Send without grant fails.
- Send does not set Service Request to `FULFILLED`.
- Status updates emit `REFERRAL_UPDATED`.
