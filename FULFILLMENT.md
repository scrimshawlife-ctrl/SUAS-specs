# FULFILLMENT.md — Service fulfillment (SUAS v0.1)

**Related:** [DISPATCH.md](DISPATCH.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md), [SETTLEMENT.md](SETTLEMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [REFERRALS.md](REFERRALS.md), [EVENT_MODEL.md](EVENT_MODEL.md)

**Actors:** Service Provider, Responder, Veteran, System.

---

## 1. Purpose

Fulfillment is the SUAS record that a Service Request was accepted, started, completed, and confirmed — or failed, partial, disputed, or cancelled.

**A Service Request is not fulfilled merely because it is assigned.**

External provider activity is represented through one or more **Fulfillment Attempts** and normalized provider status. Provider status is evidence/input to the SUAS workflow; it does not replace canonical fulfillment state.

Funding is separate from fulfillment ([SETTLEMENT.md](SETTLEMENT.md)).

---

## 2. Fulfillment states

`ACCEPTED` → `STARTED` → `COMPLETED` → `CONFIRMED` | `DISPUTED`

Exceptions: `FAILED` | `PARTIAL` | `CANCELLED`

| State | Meaning |
|---|---|
| `ACCEPTED` | Provider/responder accepted the assignment |
| `STARTED` | Work/delivery began |
| `COMPLETED` | Provider or responder marked the work done |
| `CONFIRMED` | Veteran and/or responder confirmed per §5 |
| `DISPUTED` | Veteran or responder rejected the completion claim |
| `FAILED` | Attempted; did not occur |
| `PARTIAL` | Some but not all requested support occurred |
| `CANCELLED` | Stopped before completion |

---

## 3. Fulfillment Attempt

A Fulfillment Attempt represents one deliberate attempt to obtain support from one provider adapter or manual coordination path.

A Service Request may have multiple sequential attempts. Example: Provider A unavailable → Provider B attempted → manual coordination. Those attempts do not create multiple Service Requests.

Required logical fields:

```text
fulfillment_attempt_id
service_request_id
capability
provider_adapter_id
integration_mode
idempotency_key
status
external_reference optional
last_provider_status optional
created_at
updated_at
last_checked_at optional
failure_reason optional
```

### 3.1 Attempt status

Attempt/integration status is separate from ServiceFulfillment state:

- `PROVIDER_PENDING`
- `PROVIDER_ACCEPTED`
- `PROVIDER_IN_PROGRESS`
- `PROVIDER_COMPLETED`
- `PROVIDER_DECLINED`
- `PROVIDER_CANCELLED`
- `PROVIDER_FAILED`
- `PROVIDER_UNKNOWN`
- `MANUAL_PENDING`
- `MANUAL_COMPLETED`
- `MANUAL_FAILED`

These statuses are operational evidence. They do not authorize hidden Service Request transitions.

### 3.2 Idempotency

Every external mutation for one attempt reuses the attempt's stable `idempotency_key`.

A retry of the same logical provider action must not create a second ride, room reservation, food request, or peer dispatch.

A deliberate switch to another provider creates a new Fulfillment Attempt with a new idempotency identity.

### 3.3 Unknown outcome

If an external mutation may have succeeded but SUAS did not receive a reliable response, the attempt becomes `PROVIDER_UNKNOWN`.

SUAS must reconcile by idempotency key/external reference/provider status where possible before a retry that could duplicate fulfillment. See [RESILIENCE.md](RESILIENCE.md).

---

## 4. Provider-neutral execution

MVP service categories may use:

- `TransportationPort`
- `TemporaryShelterPort`
- `FoodSupportPort`
- `PeerSupportPort`
- corresponding `ManualAdapter` paths

Provider-specific SDKs, statuses, booking objects, and webhook payloads remain inside adapters. See [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md).

A provider may operate by API, webhook, deep link, phone, email, manual coordination, or information-only mode. Lack of an API does not make a provider invalid.

Before external disclosure, SUAS evaluates Consent and creates a minimum-necessary provider projection.

---

## 5. Mapping to Service Request

| Fulfillment | Service Request (see [DISPATCH.md](DISPATCH.md)) |
|---|---|
| `ACCEPTED` | `ACCEPTED` |
| `STARTED` | `IN_PROGRESS` |
| `COMPLETED` | `FULFILLED` |
| `CONFIRMED` | `CONFIRMED` |
| `FAILED` | typically `UNFULFILLABLE` or remain actionable for another documented attempt |
| `PARTIAL` | `FULFILLED` with fulfillment status `PARTIAL`; Follow-Up may remain |
| `CANCELLED` | `CANCELLED` when the request itself is cancelled; an individual failed/cancelled attempt may instead lead to another attempt |

A `PROVIDER_COMPLETED` callback is not by itself permission to skip documented SUAS fulfillment/confirmation rules.

Do not set request `FULFILLED` from `ASSIGNED` alone.

---

## 6. Confirmation

- Veteran confirmation is preferred when reachable.
- Responder confirmation is required when the veteran cannot be reached (reason recorded) or as a second confirmation.
- MVP rule: `CONFIRMED` requires at least one of `veteran_confirmed_at` or `responder_confirmed_at`.
- If only responder confirms, reason is required.
- Dispute moves ServiceFulfillment to `DISPUTED`; the request does not move to `CONFIRMED`.

External provider completion does not replace human confirmation requirements.

---

## 7. Failure, partial, cancellation, reroute

All are first-class and require an operational reason.

A failed **Fulfillment Attempt** does not automatically mean the Service Request is unfulfillable. A responder/router may create a new attempt through another compatible provider or manual coordination.

A Service Request becomes `UNFULFILLABLE` only through the documented request transition when no acceptable path remains or policy requires closure of the request.

A Follow-Up should be scheduled unless Settlement explicitly records why no further follow-up will occur.

---

## 8. Provider webhooks

Authenticated provider webhooks may update the associated Fulfillment Attempt's normalized integration status.

They must not:

- bypass Consent;
- create a new disclosure;
- directly mutate unrelated Case/Request state;
- skip fulfillment confirmation;
- trust duplicate or out-of-order events without deduplication/reconciliation.

---

## 9. Concurrency

Creating an active provider attempt must be protected against concurrent duplicate allocation.

Implementation must define the transaction/constraint that prevents two workers/responders from accidentally initiating the same logical attempt.

Where multiple distinct attempts are allowed, each must be deliberate, separately identified, and auditable.

---

## 10. Funding

No funding/payment-card fields in MVP.

```text
Fulfillment → Funding Eligibility → Funding Source → Optional Billing Adapter
```

Status: `FUTURE`. Do not assert Medi-Cal billability.

Provider `estimated_cost` may be informational adapter data only; it must not create a payment or reimbursement workflow absent an accepted funding spec.

---

## 11. Events

Existing canonical events remain:

- `SERVICE_ACCEPTED`
- `SERVICE_FULFILLED`
- `SERVICE_FAILED`

Provider/Fulfillment Attempt telemetry may use Audit Events until additional domain events are explicitly accepted. Do not invent new canonical event names silently in implementation.

---

## 12. Testability

Required tests include:

- assigned request cannot be `FULFILLED` without fulfillment completion;
- confirm without veteran or responder timestamp fails;
- failed fulfillment is not `CONFIRMED`;
- duplicate delivery/retry of one Fulfillment Attempt does not duplicate external effect;
- provider timeout after possible acceptance becomes `PROVIDER_UNKNOWN` and reconciles before risky retry;
- failed provider attempt may reroute without creating a new Service Request;
- webhook duplicate/out-of-order handling is safe;
- provider-specific status cannot bypass Service Request transitions;
- manual adapter can satisfy each MVP capability path;
- provider data projection excludes unrelated veteran data.

---

## 13. Non-goals

- Auto-confirm on timer without a recorded actor
- Treating Referral `COMPLETED` as ServiceFulfillment
- Treating provider status as SUAS domain state
- Requiring an API-backed provider
- Selecting specific rides/rooms/food/peer vendors
- Billing or payment-card checkout
