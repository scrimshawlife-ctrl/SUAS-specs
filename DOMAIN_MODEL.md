# DOMAIN_MODEL.md — Entities and ownership (SUAS v0.1)

**Related:** [DATA_MODEL.md](DATA_MODEL.md), [GLOSSARY.md](GLOSSARY.md), [EVENT_MODEL.md](EVENT_MODEL.md), [ARCHITECTURE.md](ARCHITECTURE.md), [PRIVACY.md](PRIVACY.md), [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [FULFILLMENT.md](FULFILLMENT.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md)

**Status:** `draft` / `0.1.0`. Retention durations remain D-007 `DECISION_PENDING` unless explicitly stated.

Field sensitivity: `required` / `optional` / `sensitive`. Operational entities may be soft-deleted where specified. Domain Events and Audit Events are immutable.

---

## 1. Conventions

- **Owner:** module that may write the entity.
- **Authz:** actor visibility beyond the owner module.
- **Lifecycle:** canonical states or created → active → terminal.
- **Retention:** D-007 unless specified.
- Every tenant-owned entity carries `tenant_id`.
- Vendor-specific provider types/statuses are adapter-local and are not domain entities/enums unless normalized below.

---

## 2. Identity and organization

### User
- **Purpose:** Login identity shared across Veteran, Responder, Admin, Trusted Contact, and Service Provider users.
- **Owner:** Auth / Administration.
- **Lifecycle:** `INVITED` → `ACTIVE` → `SUSPENDED` → `REVOKED`.
- **Required:** `user_id`, `tenant_id`, `status`, `created_at`.
- **Optional/sensitive:** email, phone.
- **Rule:** revoked users cannot authenticate/act; historical event actor ids remain.

### VeteranProfile
- **Owner:** Veteran Profiles.
- **Required:** `veteran_profile_id`, `user_id`, `tenant_id`, `display_name`.
- **Optional/sensitive:** preferred language/channel, county, veteran-entered shareable notes.
- **Non-goals:** SSN, DD-214, medical history, diagnosis collection.

### Organization
- **Owner:** Administration.
- **Lifecycle:** `PENDING` → `ACTIVE` → `SUSPENDED` → `ARCHIVED`.
- **Required:** `organization_id`, `tenant_id`, `name`, `status`.
- **Optional:** contact/website/counties.

### OrganizationMembership
- **Owner:** Administration.
- **Lifecycle:** `INVITED` → `ACTIVE` → `SUSPENDED` → `REVOKED`.
- **Role:** `RESPONDER` | `ORG_ADMIN` | `SERVICE_PROVIDER_USER`.

### ResponderProfile
- **Owner:** Administration.
- **Required:** `responder_profile_id`, `user_id`, `organization_id`.
- **Optional:** display name, queue availability/capacity fields.
- **Non-goal:** clinical credential record.

---

## 3. Check-In and signal

### QuestionnaireVersion
- **Owner:** Check-ins / Administration publish path.
- **Lifecycle:** `DRAFT` → `PUBLISHED` → `SUPERSEDED`.
- Published versions are immutable.

### CheckIn
- **Owner:** Check-ins.
- **Lifecycle:** `STARTED` → `IN_PROGRESS` → `COMPLETED` | `ABANDONED` | `INCOMPLETE`.
- **Sensitive:** yes.

### CheckInResponse
- **Owner:** Check-ins.
- **Purpose:** one answer to one question for a Check-In.
- Completed history is not silently rewritten.

### SupportSignal
- **Owner:** Support Signals.
- **Lifecycle:** immutable row; override creates a new linked row.
- **Level:** `GREEN` | `YELLOW` | `ORANGE` | `RED`.
- **Rule:** deterministic/versioned; not a diagnosis.

---

## 4. Consent and trusted circle

### TrustedContact
- **Owner:** Trusted Circle.
- **Lifecycle:** `INVITED` → `ACCEPTED` → `SUSPENDED` | `REMOVED` | `REVOKED`.
- Membership alone grants no data visibility.

### ConsentGrant
- **Owner:** Consent.
- **Lifecycle:** `ACTIVE` → `REVOKED` | `EXPIRED`.
- **Purpose:** first-class, purpose-scoped permission; not a boolean.
- Evaluated at use time for share/notify/provider disclosure.

### ConsentEvent
- **Owner:** Consent.
- Immutable history: grant/revoke/expire/deny/template acceptance.

---

## 5. Cases and responder work

### SupportCase
- **Owner:** Cases.
- **Lifecycle:** `OPEN` → `TRIAGED` → `ASSIGNED` → `ACTIVE` → `FOLLOWUP` → `RESOLVED` → `CLOSED`, subject to documented transitions.
- **Rule:** claim/assignment under contention must have one deterministic winner where exclusive ownership applies.

### CaseAssignment
- **Owner:** Cases.
- **Lifecycle:** `ACTIVE` → `RELEASED` | `REASSIGNED`.
- Assignment is not Fulfillment.

### CaseNote
- **Owner:** Cases.
- Timestamped responder note; not a Follow-Up, Settlement, or Contact Attempt.
- Veteran full-note visibility denied for MVP default while D-015 remains open.

### ContactAttempt
- **Owner:** Cases.
- First-class responder contact log; not a Case Note.
- Required channel/outcome/timestamp/actor semantics in responder/API specs.

### FollowUp
- **Owner:** Follow-up.
- **Lifecycle:** `SCHEDULED` → `DUE` → `COMPLETED` | `RESCHEDULED` | `OVERDUE` → `ESCALATED` | `CANCELLED`.

---

## 6. Requests, providers, resources

### ServiceRequest
- **Owner:** Requests / Dispatch.
- **Purpose:** one specific requested need inside a Support Case.
- **Category:** MVP `FOOD` | `TRANSPORTATION` | `SHELTER` | `PEER_SUPPORT`.
- **Lifecycle:** `CREATED` → `SUBMITTED` → `TRIAGED` → `MATCHING` → `ASSIGNED` → `ACCEPTED` → `IN_PROGRESS` → `FULFILLED` → `CONFIRMED` → `CLOSED` plus documented exceptions.
- Provider integration status is not this lifecycle.

### ServiceProvider
- **Owner:** Administration / Resources.
- **Purpose:** person/organization capable of fulfilling a Service Request.
- **Lifecycle:** `ACTIVE` → `SUSPENDED` → `ARCHIVED`.
- A valid provider may have no API.

### ServiceOffer
- **Owner:** Resources.
- **Purpose:** provider offering for a category/capability.
- **Required:** provider, category, active state.
- **Optional:** capacity, hours, supported integration modes.

### Resource
- **Owner:** Resources.
- **Purpose:** catalog entry for support availability.
- **Required:** resource/provider/org linkage as applicable, category, service name, active state, freshness verification.
- **Optional:** eligibility, counties/coverage, hours, contact/referral method, cost/capacity, integration modes.

### ResourceCategory
- **Owner:** Resources.
- MVP codes: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.
- Future codes remain reserved as specified in [PRODUCT.md](PRODUCT.md).

---

## 7. Provider integration entities

### ProviderAdapterConfiguration
- **Owner:** Provider Router / Administration.
- **Purpose:** binds an environment/tenant/provider/capability to an enabled adapter without making the vendor part of the domain model.
- **Required:** configuration id, tenant scope, adapter id, capability, integration mode, enabled state.
- **Optional:** service provider, coverage scope, priority/routing metadata.
- **Sensitive:** secrets are **not** stored on this entity; credentials remain deployment secrets.
- **Authz:** SUAS-admin; scoped org-admin only where provider configuration authority is explicitly granted.

### FulfillmentAttempt
- **Owner:** Fulfillment / Provider Router.
- **Purpose:** one deliberate attempt to obtain support from one provider adapter or manual path.
- **Required:** `fulfillment_attempt_id`, `service_request_id`, `tenant_id`, capability, provider adapter id, integration mode, stable idempotency key, attempt status, created time.
- **Optional:** service provider, external reference, last normalized provider status, last checked time, failure reason.
- **Attempt status:** `PROVIDER_PENDING` | `PROVIDER_ACCEPTED` | `PROVIDER_IN_PROGRESS` | `PROVIDER_COMPLETED` | `PROVIDER_DECLINED` | `PROVIDER_CANCELLED` | `PROVIDER_FAILED` | `PROVIDER_UNKNOWN` | `MANUAL_PENDING` | `MANUAL_COMPLETED` | `MANUAL_FAILED`.
- **Rules:**
  - retry of the same logical attempt reuses idempotency identity;
  - deliberate reroute/provider switch creates a new FulfillmentAttempt;
  - ambiguous external outcome becomes `PROVIDER_UNKNOWN` and reconciles before a risky retry;
  - provider-specific raw statuses remain adapter-local.
- **Spec:** [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [FULFILLMENT.md](FULFILLMENT.md), [RESILIENCE.md](RESILIENCE.md).

### ProviderOffer
- **Purpose:** normalized, ephemeral-or-persisted offer shape returned through provider ports.
- **Owner:** Provider Router / Fulfillment.
- **Required logical fields:** provider/adapter ref, capability, Service Request, availability status, fulfillment mode.
- **Optional:** estimated start/end/cost, expiry, external reference, cancellation support, freshness metadata.
- **Rule:** provider offer cost is informational only absent an accepted funding/billing spec.

---

## 8. Fulfillment and referral

### ServiceFulfillment
- **Owner:** Fulfillment.
- **Purpose:** SUAS record of acceptance/start/completion/confirmation/failure/partial/cancel.
- **Lifecycle:** `ACCEPTED` → `STARTED` → `COMPLETED` → `CONFIRMED` | `DISPUTED`, or `FAILED` | `PARTIAL` | `CANCELLED`.
- **Optional link:** FulfillmentAttempt that supplied fulfillment evidence.
- **Rule:** provider completion cannot bypass SUAS confirmation/authorization semantics.
- Funding fields remain `FUTURE`.

### Referral
- **Owner:** Referrals.
- **Purpose:** directed handoff distinct from Service Request/Fulfillment.
- **Lifecycle:** `DRAFTED` → `SENT` → `ACKNOWLEDGED` → `ACCEPTED` | `DECLINED` → `COMPLETED` | `UNABLE_TO_SERVE` | `CANCELLED`.
- Requires applicable Consent Grant at send time.

---

## 9. Notifications

### Notification
- **Owner:** Notifications.
- **Purpose:** one logical send, not one row per retry.
- **Delivery state:** `QUEUED` → `SENT` | `FAILED` → `DELIVERED` | `BOUNCED` | `UNDELIVERABLE`.
- Each send attempt appends immutable Audit Event.
- Channel provider status does not change domain workflow state.

### NotificationPreference
- **Owner:** Notifications.
- Channel preference only; cannot grant consent.

---

## 10. Audit and events

### AuditEvent
- **Owner:** Audit/Event Layer.
- Immutable who/what/when/target record.

### DomainEvent
- **Owner:** Audit/Event Layer.
- Immutable canonical business fact using [EVENT_MODEL.md](EVENT_MODEL.md).

Provider attempt telemetry may remain Audit Events until specific new domain event names are accepted. Implementation must not invent canonical event names silently.

---

## 11. Pilot and feedback

### Pilot
- **Owner:** Administration.
- Current controlled pilot: Santa Clara County, approximately 25–50 veterans.
- Pilot size is an operating scope, not a production architecture ceiling.

### PilotEnrollment
- **Owner:** Veteran Profiles / Administration.
- **Lifecycle:** `APPLIED` → `ENROLLED` → `WITHDRAWN` | `COMPLETED` | `REMOVED`.

### Feedback
- **Owner:** Administration.
- Operational/satisfaction feedback, not clinical instrument.

---

## 12. Cross-cutting invariants

1. Every tenant-owned entity carries tenant scope.
2. Consent is evaluated at use time; provider disclosure is also minimum-necessary projected.
3. Domain/authorization correctness cannot depend on one app process.
4. Provider SDK types/statuses/payloads do not become domain types.
5. ServiceProvider does not imply API capability.
6. Service Request state is independent of provider-attempt state.
7. Assignment is not Fulfillment; ProviderOffer is not Fulfillment; Referral completion is not automatically Fulfillment.
8. External mutation retries are idempotent per FulfillmentAttempt.
9. Duplicate/out-of-order provider callbacks cannot silently corrupt canonical state.
10. Contested exclusive commands are atomic.
11. Audit/Domain Events remain immutable.
12. Mutable list/history entities must support bounded/paginated production access as they grow.
