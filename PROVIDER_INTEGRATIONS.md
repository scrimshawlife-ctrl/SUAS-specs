# PROVIDER_INTEGRATIONS.md — Provider-neutral fulfillment integration contract (SUAS v0.1.3)

**Lifecycle:** `released` via [RELEASE_MANIFEST-0.1.3.md](RELEASE_MANIFEST-0.1.3.md)
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
10. A provider name is canonical only when explicitly recorded as a released decision. D-017 v0.1.2 records Uber behind `TransportationPort`; D-018 v0.1.3 records Amadeus as the first commercial shelter search/inventory adapter family behind `TemporaryShelterPort`. Neither changes domain semantics.
11. A commercial search result is not fulfillment, a hold is not a confirmed reservation, and provider reservation status is not canonical SUAS state.
12. `ManualShelterAdapter` is mandatory even when Amadeus search/inventory is configured.
13. SUAS MUST NOT collect, transmit, proxy, tokenize, or store raw payment-card data for shelter fulfillment.

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

Three "mode" concepts are drawn from this same enum but are independent (0.1.4): `Resource.integration_modes` is the catalog capability *set* a Resource declares; `FulfillmentAttempt.integration_mode` is the single mode used for *one* attempt; and `ProviderOffer.fulfillment_mode` is the mode of a *specific offer*. No subset or implication relationship holds between them — a Resource declaring several modes does not require any particular attempt or offer mode, and an attempt/offer mode need not appear in the Resource set beyond what routing policy selects.

---

## 4. Capability ports

The following port names are logical examples. Exact programming-language interface names may vary, but semantics must not.

### 4.0 Mandatory manual adapter and registry (0.1.4)

Each capability has a mandatory manual adapter named per capability — `ManualShelterAdapter`, `ManualTransportationPort` adapter, `ManualFoodAdapter`, `ManualPeerSupportAdapter` — rather than a single bare "Manual Adapter". Adapters are addressed through a per-capability registry keyed by capability + adapter id, so routing selects a concrete adapter within a capability. Invariant 8 (manual coordination always available) is satisfied by the manual adapter's guaranteed presence in every capability's registry.

### 4.1 TransportationPort

Supports transportation coordination without assuming a rideshare business model. D-017 v0.1.2 selects Uber as the first API-backed transportation adapter family that may implement this port. That selection does not make Uber concepts canonical, does not remove Manual/Fake adapters, and does not authorize real bookings before SPEC-018 readiness.

Minimum capability vocabulary:

- `search_options(request_context)`
- `request_quote(option)` when provider supports quoting
- `request_transport(option, idempotency_key)`
- `get_status(external_reference)`
- `cancel(external_reference, idempotency_key)` when supported

The port must support providers that require responder confirmation, phone dispatch, voucher issuance, fare support, volunteer rides, or direct booking.

### 4.2 TemporaryShelterPort

Supports temporary shelter/accommodation coordination. It MUST NOT redefine MVP `SHELTER` as permanent housing placement. D-018 v0.1.3 selects Amadeus as the first commercial search/inventory adapter family that may implement this port. The selection does not make Amadeus property, rate, offer, payment, or reservation concepts canonical.

Minimum capability vocabulary:

- `search_availability(request_context)`
- `hold(option, idempotency_key)` when supported
- `reserve(option, idempotency_key)` when supported
- `get_status(external_reference)`
- `cancel(external_reference, idempotency_key)` when supported

Provider offers may require manual or telephone confirmation.

`ManualShelterAdapter` is mandatory. It remains available when commercial search is absent, degraded, payment-blocked, contractually unsupported, or inappropriate for the request.

Search results MUST be normalized before routing/display and ranked deterministically from versioned explicit inputs. Each ranked result records bounded explainability reason codes and uses a stable documented tie-breaker. Allowed ranking inputs are explicit availability, geography/coverage, requested stay window, recorded accessibility fit, cancellation terms, and informational cost. Ranking MUST NOT infer clinical need, hidden eligibility, household facts, or ability to pay, and MUST NOT present inventory as guaranteed availability or a completed reservation.

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

## 4.5 D-017 Uber adapter constraints

An Uber transportation adapter MUST:

1. live behind `TransportationPort` and the Provider Router;
2. keep Uber SDKs, payloads, quote details, webhook signatures, status values, and booking identifiers adapter-local;
3. normalize all provider statuses to integration-level statuses before they can inform canonical Fulfillment workflows;
4. require SUAS-side idempotency for every quote/request/cancel external mutation;
5. disclose only consented, minimum-necessary ride-operational data;
6. preserve manual coordination and fake/test adapters;
7. fail closed if credentials, webhook verification, environment authorization, or SPEC-018 production readiness are absent;
8. avoid defining funding, reimbursement, payment-card, maps/geocoding, SLO, capacity, RTO, or RPO policy.

---

## 4.6 D-018 Amadeus shelter adapter constraints

An Amadeus temporary-shelter adapter MUST:

1. live behind `TemporaryShelterPort` and the Provider Router;
2. keep Amadeus SDKs, endpoints, credentials, property/rate/offer identifiers, availability payloads, statuses, and reservation objects adapter-local;
3. normalize search/inventory results before SUAS-owned offer/ranking flows;
4. preserve `ManualShelterAdapter` and fake/test adapters;
5. implement deterministic, explainable ranking under §4.2 and retain the ranking rule/input identity and bounded reason codes for audit;
6. disclose only the released field-level shelter projection in §13.2 after use-time consent evaluation;
7. use a stable FulfillmentAttempt idempotency identity for every hold, reserve, or cancel mutation;
8. record ambiguous mutation outcomes as `PROVIDER_UNKNOWN` and reconcile before duplicate-risk retry;
9. publish normalized health/degradation state and preserve truthful fallback/manual continuity;
10. fail closed if credentials, environment authorization, callback/webhook validation where applicable, or SPEC-018 readiness are absent;
11. never collect, transmit, proxy, tokenize, or store raw payment-card data;
12. report reservation as `BLOCKED_BY_PAYMENT_ARCHITECTURE` unless a documented, owner-approved card-free enterprise contract permits the selected reservation path with no SUAS raw-card handling;
13. avoid defining funding, reimbursement, deposits, incidentals, guarantees, cancellation-fee policy, eligibility, minors/guardian, identity-document, SLO, capacity, RTO, or RPO semantics.

Search/inventory may remain available while reservation is blocked. Capability support must be reported truthfully, and the adapter must not fake reservation success.

`BLOCKED_BY_PAYMENT_ARCHITECTURE` is an explicit adapter/application capability-block reason. It is not a new canonical Service Request, Fulfillment, or provider integration status. The Service Request remains actionable through an authorized manual or alternate path.

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

For shelter, `MISCONFIGURED`, payment-dependent reservation, unsupported mutation, ambiguous outcome, rate limiting, or provider outage preserves the Service Request and exposes `ManualShelterAdapter` when policy permits. Search may remain informational while reservation is blocked, but that limitation must be stated truthfully.

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

### 13.1 Released `TRANSPORTATION` projection (D-017 v0.1.2)

The SUAS-owned transportation projection may contain only:

- `rider`: `firstName`, `lastName`, and E.164 `phoneNumber`;
- `pickup`: `latitude`, `longitude`, and optional dispatch address;
- `dropoff`: `latitude`, `longitude`, and optional dispatch address;
- optional `productId` selected from a current provider estimate;
- optional `noteForDriver` limited to necessary accessibility or pickup instructions.

This projection requires an active, use-time `can_share` Consent Grant for
`service_request_fulfillment` to the selected adapter. `who_pays`, payment-card
data, Case Notes, Check-In answers, Support Signal basis, Trusted Circle data,
medical or military records, SSNs, unrelated requests, and continuous location
history remain outside the provider projection. Provider-specific DTO names and
payloads remain adapter-local.

### 13.2 Released `SHELTER` projection (D-018 v0.1.3)

The SUAS-owned temporary-shelter search projection may contain only:

- requested `check_in_date` and `check_out_date`;
- requested locality/coverage as city, county, postal code, or bounded search coordinates/region when required;
- `room_count` and `guest_count` when explicitly supplied for this request;
- recorded accessibility requirements limited to accommodation matching;
- explicit request-scoped shelter constraints represented as bounded codes rather than Case/Check-In free text.

The reservation projection, only when a documented card-free enterprise contract makes reservation permissible, may additionally contain:

- selected adapter-local offer reference;
- guest `first_name` and `last_name`;
- one E.164 phone number and/or email address required for reservation/contact;
- a necessary bounded accessibility or arrival note;
- opaque `service_request_id` / `fulfillment_attempt_id` correlation references where accepted.

This projection requires an active, use-time `can_share` Consent Grant for `service_request_fulfillment` to the selected adapter. Raw payment-card data, payment security codes, payment tokens/forms, government identity documents, unrelated addresses, Case Notes, Check-In answers, Support Signal level/basis, Trusted Circle data, medical/military records, SSNs, unrelated requests, and unbounded free text remain outside the projection.

If the provider requires a field outside this projection, the operation is not production-ready and returns to a human/spec decision rather than expanding disclosure implicitly.

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

Specific providers remain `DECISION_PENDING` unless named in [DECISIONS.md](DECISIONS.md). D-017 names Uber for transportation, and D-018 names Amadeus for temporary-shelter search/inventory. Food and external peer-support providers remain pending.

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
- routing can replace one adapter with another without domain changes;
- shelter search ranking is deterministic and explainable with bounded reason codes;
- payment-dependent shelter reservation fails closed as `BLOCKED_BY_PAYMENT_ARCHITECTURE`, and any card-free enterprise path has documented authority and no raw-card handling.

Current status: `NOT_READY`.

---

## 17. Non-goals

- Selecting additional rides vendors
- Selecting additional hotel/rooms vendors
- Selecting a food-delivery vendor
- Requiring all providers to expose an API
- Embedding provider pricing or eligibility as product law
- Letting provider state replace SUAS state
- Building payment-card checkout in MVP
