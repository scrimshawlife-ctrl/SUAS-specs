# REFERRALS.md — Referrals (distinct from Service Requests)

**Status:** `draft` / `0.1.0` / SPEC-005 preflight; not implementation authority.  
**Related:** [DISPATCH.md](DISPATCH.md), [RESOURCES.md](RESOURCES.md), [CONSENT.md](CONSENT.md), [FOLLOWUP.md](FOLLOWUP.md), [FULFILLMENT.md](FULFILLMENT.md), [EVENT_MODEL.md](EVENT_MODEL.md), [RESILIENCE.md](RESILIENCE.md)

**Actors:** Responder, destination Organization / Service Provider, Veteran, System.

---

## 1. Purpose

A **Referral** is a directed, consented handoff to a destination Resource or Service Provider. It is not a Service Request. Sending a Referral is not Fulfillment and is not evidence that service was received.

---

## 2. Required content

- `consent_grant_id` or documented accepted system basis that authorizes the destination/payload at send time;
- destination type/id;
- reason;
- method (`IN_APP`, `PHONE`, `EMAIL`, or accepted future method);
- status;
- result when known;
- linked Follow-Up when check-back is required;
- optional `service_request_id` when the referral supports a Service Request.

A Referral does not auto-transition a Service Request to `FULFILLED`.

---

## 3. States

`DRAFTED` → `SENT` → `ACKNOWLEDGED` → `ACCEPTED` | `DECLINED` → `COMPLETED` | `UNABLE_TO_SERVE` | `CANCELLED`

| Transition | Actor | Preconditions / meaning |
|---|---|---|
| → `DRAFTED` | Responder | draft may exist without disclosure |
| `DRAFTED` → `SENT` | Responder/System send path | use-time consent/basis + minimum-necessary projection + idempotent send identity |
| `SENT` → `ACKNOWLEDGED` | Destination / Responder-on-behalf | acknowledgment evidence |
| `ACKNOWLEDGED` → `ACCEPTED` / `DECLINED` | Destination / Responder-on-behalf | decline reason when applicable |
| `ACCEPTED` → `COMPLETED` / `UNABLE_TO_SERVE` | Destination / Responder | destination reports result; still not ServiceFulfillment by itself |
| allowed non-terminal → `CANCELLED` | Veteran / Responder | authority + reason |

No hidden status mutation from delivery provider callbacks.

---

## 4. Consent and minimum disclosure

Every outbound disclosure is evaluated at the moment of send.

- Draft creation does not authorize later send.
- Grant revocation before send blocks send.
- A prior send does not authorize expansion of payload or disclosure to a new destination.
- Destination receives only fields required for the referral purpose.
- Check-In answers, full Support Signal basis, Case Notes, Contact Attempts, and unrelated requests are excluded unless an explicit accepted grant/purpose requires them.

---

## 5. Send idempotency / ambiguous delivery

Referral send is a retryable external effect and must have a stable logical send/idempotency identity.

Rules:

1. Replaying the same send command does not create a duplicate logical Referral or intentionally disclose twice.
2. Email/SMS/in-app delivery retries are notification/integration attempts, not new Referral state transitions.
3. If transport outcome is ambiguous, SUAS records the Referral as sent/pending delivery according to the accepted notification integration semantics and does not create another Referral to represent uncertainty.
4. A deliberate resend to the same destination after a documented failure is auditable and re-checks consent/basis.
5. A deliberate new destination creates a new Referral unless a later accepted multi-destination model says otherwise.
6. Transport/provider callbacks may update delivery evidence but cannot mark `ACKNOWLEDGED`, `ACCEPTED`, or `COMPLETED` unless they represent authenticated destination-level evidence accepted by the contract.

---

## 6. Follow-Up relationship

A Referral that requires confirmation/check-back creates or links a first-class Follow-Up.

Referral delivery retry is not Follow-Up retry. Follow-Up coordination attempts are tracked under [FOLLOWUP.md](FOLLOWUP.md).

`REFERRAL COMPLETED` does not auto-complete linked Follow-Up unless the Follow-Up's explicit completion condition is satisfied.

---

## 7. Events

Canonical:

- `REFERRAL_CREATED`
- `REFERRAL_UPDATED`
- `FOLLOWUP_CREATED` when a linked Follow-Up is created

Rules:

- first persisted draft/send semantics must not emit duplicate `REFERRAL_CREATED` on replay;
- later valid status changes emit/refer to `REFERRAL_UPDATED` according to the event contract;
- delivery-attempt telemetry may be Audit/Notification events and does not require new Referral Domain Events.

---

## 8. Non-goals

- equating send with service received;
- silent email/fax/API integrations with unnamed vendors;
- whole-Case disclosure;
- using a delivery receipt as provider service acceptance unless the destination protocol explicitly proves it;
- duplicate Referral creation as a retry mechanism;
- treating Referral completion as ServiceFulfillment.

---

## 9. Testability

- send without valid consent/basis fails;
- reroute/new destination re-checks consent;
- duplicate send command does not duplicate logical Referral/disclosure event;
- transport retry does not produce a new Referral state transition;
- send does not set Service Request `FULFILLED`;
- destination delivery receipt alone cannot set `ACKNOWLEDGED`/`COMPLETED` unless contractually authenticated as such;
- `COMPLETED` Referral does not create ServiceFulfillment automatically;
- linked Follow-Up remains independent until explicitly completed/cancelled.
