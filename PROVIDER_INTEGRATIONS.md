# PROVIDER_INTEGRATIONS.md — Provider-neutral fulfillment integration contract (SUAS v0.1)

**Status:** `draft` / `0.1.0` / not implementation authority until accepted and released.  
**Related:** [APIS.md](APIS.md), [ARCHITECTURE.md](ARCHITECTURE.md), [DISPATCH.md](DISPATCH.md), [FULFILLMENT.md](FULFILLMENT.md), [RESOURCES.md](RESOURCES.md), [CONSENT.md](CONSENT.md), [SECURITY.md](SECURITY.md), [TESTING.md](TESTING.md), [DECISIONS.md](DECISIONS.md)

---

## 1. Purpose

SUAS must coordinate external services without binding the product domain to a specific rides, rooms/shelter, food, peer-support, or communications vendor.

The production architecture therefore treats external service integrations as **capability ports** with replaceable adapters. Provider selection is an environment/organization/routing decision, not a rewrite of Service Request, Fulfillment, or Case semantics.

A Service Provider does not need an API to be valid. Manual and referral-only providers are first-class.

---

## 2. Governing invariants

1. Domain modules MUST NOT import provider SDKs.
2. Vendor-specific request/response objects MUST NOT cross an adapter boundary.
3. Provider-specific status values MUST NOT become canonical SUAS Service Request states.
4. Every external mutation MUST be idempotent from the SUAS side.
5. Consent and minimum-necessary disclosure MUST be evaluated before transmitting veteran data.
6. Provider outage MUST NOT erase, close, or silently fail a Service Request.
7. A provider may be replaced without changing the canonical Service Request or Fulfillment state machines.
8. Manual coordination MUST remain available where an API is absent or unavailable.
9. Routing by geography, organization, capability, health, capacity, or funding policy MUST occur above the provider adapter.
10. No provider name is canonical until explicitly recorded as a deployment/partner decision.

---

## 3. Integration modes

Every Resource/Service Provider may declare one or more integration modes:

- `API`
- `WEBHOOK`
- `DEEP_LINK`
- `PHONE`
- `EMAIL`
- `MANUAL_COORDINATION`
- `NONE`

`NONE` means SUAS can display/reference the resource but has no direct fulfillment integration.

An API-backed provider is not inherently preferred over a manual provider. Operational policy determines selection.

---

## 4. Capability ports

The following port names are logical examples. Exact programming-language interface names may vary, but semantics must not.

### 4.1 TransportationPort

Supports transportation coordination without assuming a rideshare business model.

Minimum capability vocabulary:

- `search_options(request_context)`
- `request_quote(option)` when provider supports quoting
- `request_transport(option, idempotency_key)`
- `get_status(external_reference)`
- `cancel(external_reference, idempotency_key)` when supported

The port must support providers that require responder confirmation, phone dispatch, voucher issuance, fare support, volunteer rides, or direct booking.

### 4.2 TemporaryShelterPort

Supports temporary shelter/accommodation coordination. It MUST NOT redefine MVP `SHELTER` as permanent housing placement.

Minimum capability vocabulary:

- `search_availability(request_context)`
- `hold(option, idempotency_key)` when supported
- `reserve(option, idempotency_key)` when supported
- `get_status(external_reference)`
- `cancel(external_reference, idempotency_key)` when supported

Provider offers may require manual or telephone confirmation.

### 4.3 FoodSupportPort

Minimum capability vocabulary:

- `search_resources(request_context)`
- `check_availability(resource)` when supported
- `request_support(resource, idempotency_key)` when supported
- `get_status(external_reference)` when supported
- `cancel(external_reference, idempotency_key)` when supported

Food resources may be catalog/referral-only and have no transactional API.

### 4.4 PeerSupportPort

Supports human/peer coordination, not therapy or clinical care.

Minimum capability vocabulary:

- `find_available(request_context)` when provider supplies availability
- `request_contact(option, idempotency_key)`
- `acknowledge(external_reference)` when supported
- `get_status(external_reference)` when supported
- `cancel(external_reference, idempotency_key)` when supported

A local responder/QRF workflow may implement this port internally without an external vendor.

---

## 5. Provider Router

Domain modules call the capability port through a Provider Router.

```text
ServiceRequest
      |
      v
Fulfillment Orchestrator
      |
      v
Provider Router
   |-- Adapter A
   |-- Adapter B
   |-- Adapter C
   `-- Manual Adapter
```

The router may evaluate only explicit operational inputs such as:

- service category/capability;
- geography/coverage;
- organization/tenant configuration;
- provider health;
- provider capacity when available;
- availability window;
- funding policy if an accepted future funding spec permits it;
- responder-selected provider;
- configured preference/priority.

The router MUST NOT make clinical judgments or silently infer eligibility not defined in accepted specs.

Routing rules must be versioned/configurable and auditable when they alter which provider receives a veteran-data disclosure.

---

## 6. Normalized provider offer

Adapters normalize provider-specific data into a SUAS-owned shape. At minimum:

```text
ProviderOffer
- provider_ref
- adapter_ref
- capability
- service_request_id
- availability_status
- fulfillment_mode
- estimated_start
- estimated_end
- estimated_cost            optional / informational only unless funding specs authorize use
- funding_required          optional / unknown allowed
- expires_at                optional
- external_reference        optional until a provider-side object exists
- consent_requirements
- cancellation_supported
- source_freshness
- metadata                  bounded, non-authoritative adapter data
```

Provider metadata must not become an unbounded dumping ground for sensitive payloads.

---

## 7. Fulfillment modes

An offer/provider response must normalize to one of:

- `DIRECT_BOOKING`
- `PROVIDER_CONFIRMATION`
- `PHONE_CONFIRMATION`
- `REFERRAL_REQUIRED`
- `MANUAL_COORDINATION`
- `INFORMATION_ONLY`
- `UNAVAILABLE`

This prevents the product from assuming that every nonprofit, hotel, transportation service, pantry, or peer organization operates like an ecommerce API.

---

## 8. External status normalization

Provider-specific statuses map inside the adapter to the following integration-level statuses:

- `PROVIDER_PENDING`
- `PROVIDER_ACCEPTED`
- `PROVIDER_IN_PROGRESS`
- `PROVIDER_COMPLETED`
- `PROVIDER_DECLINED`
- `PROVIDER_CANCELLED`
- `PROVIDER_FAILED`
- `PROVIDER_UNKNOWN`

These are **integration statuses**, not replacements for the canonical Service Request state machine in [DISPATCH.md](DISPATCH.md).

Example: a provider returning `PROVIDER_COMPLETED` may supply evidence for a responder to perform the documented SUAS fulfillment transition; the webhook itself must not bypass authorization, consent, or confirmation rules.

---

## 9. Fulfillment attempts and idempotency

Every provider-side mutation uses a stable SUAS idempotency key derived from an immutable Fulfillment Attempt identity.

Required logical identity:

```text
fulfillment_attempt_id
service_request_id
capability
provider_adapter_id
idempotency_key
created_at
status
external_reference
last_provider_status
last_checked_at
```

A retry of the same attempt MUST reuse the same idempotency key. A deliberate new provider attempt MUST create a new `fulfillment_attempt_id`.

This protects against duplicate rides, duplicate reservations, duplicate food requests, and repeated peer dispatch caused by retries or concurrent workers.

---

## 10. Concurrency

Provider integrations must assume that:

- multiple responders may inspect the same Service Request;
- jobs may be delivered more than once;
- webhooks may be delivered more than once or out of order;
- provider calls may timeout after the provider accepted the request;
- a user may retry after an ambiguous response.

Therefore:

1. SUAS state transitions are atomic.
2. Provider mutation attempts are idempotent.
3. Webhook processing is deduplicated by provider event id or a deterministic adapter key.
4. Ambiguous timeouts become `PROVIDER_UNKNOWN`, not a blind second booking.
5. Reconciliation may query provider status before another mutation is attempted.

---

## 11. Webhooks

Provider webhooks are adapter-local ingress.

Rules:

- authenticate signatures/secrets or equivalent;
- reject unverifiable callbacks;
- deduplicate delivery;
- tolerate out-of-order events;
- map into normalized provider status;
- persist only the minimum payload needed for operation/audit;
- never let a webhook authorize a new disclosure;
- never let a webhook bypass the canonical Service Request/Fulfillment transition rules;
- record adapter/provider reference, event id, received time, normalized outcome, and processing result.

Exact vendor webhook paths and payload schemas are non-canonical adapter configuration.

---

## 12. Resilience and degradation

Each provider adapter must publish runtime health states such as:

- `HEALTHY`
- `DEGRADED`
- `RATE_LIMITED`
- `UNAVAILABLE`
- `MISCONFIGURED`

Provider Router behavior must be explicit for each state.

Where allowed by policy, an unavailable API may degrade to another compatible adapter or `MANUAL_COORDINATION`. SUAS must not report fulfillment merely because an adapter failed.

Circuit breaking, bounded retries, timeout limits, and backoff are required for external network calls. Retry behavior must respect provider rate-limit headers when available.

See [RESILIENCE.md](RESILIENCE.md).

---

## 13. Privacy and consent projection

Before an adapter call, SUAS builds a provider-specific disclosure projection containing only fields required for the agreed purpose.

Adapters MUST NOT receive by default:

- full Check-In answers;
- full Case Notes;
- Trusted Circle membership;
- unrelated Service Requests;
- medical history;
- SSN or military records;
- continuous location history.

If a provider needs location, contact, accessibility, or destination data, the capability contract must identify the field and applicable Consent Grant purpose.

Revocation stops future provider disclosures where the workflow permits; historical provider-side records already lawfully transmitted cannot be silently erased by SUAS unless an accepted retention/deletion process supports that action.

---

## 14. Configuration and secrets

Provider credentials are deployment secrets, not source code or canonical specs.

Configuration may identify:

- adapter id;
- capability;
- environment;
- tenant/org scope;
- geography/coverage;
- health-check mode;
- rate-limit policy;
- timeout/retry policy;
- enabled/disabled state.

Secrets MUST NOT be stored in Resource rows or client-visible configuration.

---

## 15. Provider decisions

Specific providers for transportation, temporary shelter/rooms, food, and peer support remain `DECISION_PENDING` unless named in [DECISIONS.md](DECISIONS.md).

Choosing a provider must not require changing this contract. A selected provider is an adapter/configuration decision plus any partner/legal/compliance review required for that data flow.

---

## 16. Acceptance gate: EXTERNAL FULFILLMENT

`EXTERNAL_FULFILLMENT = READY` only when:

- capability ports exist for every enabled external MVP service integration;
- domain code has no provider SDK imports;
- a Manual Adapter exists;
- every enabled adapter passes the common conformance suite;
- duplicate provider mutation tests prove idempotency;
- ambiguous timeout reconciliation is tested;
- provider webhook authentication/deduplication is tested;
- provider failure does not lose/close Service Requests;
- consent/minimum-necessary projection is tested;
- provider status mapping cannot bypass canonical transitions;
- routing can replace one adapter with another without domain changes.

Current status: `NOT_READY`.

---

## 17. Non-goals

- Selecting a rides vendor
- Selecting a hotel/rooms vendor
- Selecting a food-delivery vendor
- Requiring all providers to expose an API
- Embedding provider pricing or eligibility as product law
- Letting provider state replace SUAS state
- Building payment-card checkout in MVP
