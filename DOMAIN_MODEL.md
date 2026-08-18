# DOMAIN_MODEL.md — Entities and ownership (SUAS v0.1)

**Related:** [DATA_MODEL.md](DATA_MODEL.md), [GLOSSARY.md](GLOSSARY.md), [EVENT_MODEL.md](EVENT_MODEL.md), [ARCHITECTURE.md](ARCHITECTURE.md), [PRIVACY.md](PRIVACY.md), [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [FULFILLMENT.md](FULFILLMENT.md), [SETTLEMENT.md](SETTLEMENT.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md)

**Status:** `draft` / `0.1.0`. Retention durations remain D-007 `DECISION_PENDING` unless explicitly stated. SPEC-006 remains dependency-blocked; this file is preflight reconciliation, not acceptance.

---

## 1. Conventions

- **Owner:** module that may write the entity.
- **Authz:** actor visibility beyond the owner module.
- **Lifecycle:** canonical states or created → active → terminal.
- **Retention:** D-007 unless specified.
- Every tenant-owned entity carries `tenant_id`.
- Vendor-specific provider types/statuses remain adapter-local unless normalized by an accepted SUAS contract.
- A **current projection** is derived from durable history by an explicit deterministic rule; insertion order alone is never authority.

---

## 2. Identity and organization

### User
- **Owner:** Auth / Administration.
- **Lifecycle:** `INVITED` → `ACTIVE` → `SUSPENDED` → `REVOKED`.
- **Required:** `user_id`, `tenant_id`, `status`, `created_at`.
- **Optional/sensitive:** email, phone.
- Revoked users cannot authenticate/act; historical actor ids remain.

### VeteranProfile
- **Owner:** Veteran Profiles.
- **Required:** `veteran_profile_id`, `user_id`, `tenant_id`, `display_name`.
- **Optional/sensitive:** preferred language/channel, county, veteran-entered shareable notes.
- **Non-goals:** SSN, DD-214, medical history, diagnosis collection.

### Organization
- **Owner:** Administration.
- **Lifecycle:** `PENDING` → `ACTIVE` → `SUSPENDED` → `ARCHIVED`.
- **Required:** `organization_id`, `tenant_id`, `name`, `status`.

### OrganizationMembership
- **Owner:** Administration.
- **Lifecycle:** `INVITED` → `ACTIVE` → `SUSPENDED` → `REVOKED`.
- **Role:** `RESPONDER` | `ORG_ADMIN` | `SERVICE_PROVIDER_USER`.

### ResponderProfile
- **Owner:** Administration.
- **Required:** `responder_profile_id`, `user_id`, `organization_id`.
- **Optional:** display name, queue availability/capacity fields.

---

## 3. Check-In and signal

### QuestionnaireVersion
- **Owner:** Check-ins / Administration publish path.
- **Lifecycle:** `DRAFT` → `PUBLISHED` → `SUPERSEDED`.
- Published versions are immutable and become visible atomically as a complete version.

### CheckIn
- **Owner:** Check-ins.
- **Lifecycle:** `STARTED` → `IN_PROGRESS` → `COMPLETED` | `ABANDONED` | `INCOMPLETE`.
- Completion is idempotent at the logical command/event level.

### CheckInResponse
- **Owner:** Check-ins.
- One answer to one question for a Check-In.
- Completed response history is not silently rewritten.

### SupportSignal
- **Owner:** Support Signals.
- **Lifecycle:** immutable row; override creates a new linked row.
- **Level:** `GREEN` | `YELLOW` | `ORANGE` | `RED`.
- **Primary computation identity:** stable across duplicate/replayed jobs for the same source + signal version + questionnaire version.
- **Effective projection:** deterministic from accepted primary/override semantics; must not depend on insertion order alone.
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
- First-class, purpose-scoped permission; evaluated at use time for share/notify/provider disclosure.

### ConsentEvent
- **Owner:** Consent.
- Immutable history: grant/revoke/expire/deny/template acceptance.

---

## 5. Cases and responder work

### SupportCase
- **Owner:** Cases.
- **Lifecycle:** `OPEN` → `TRIAGED` → `ASSIGNED` → `ACTIVE` → `FOLLOWUP` → `RESOLVED` → `CLOSED`, subject to [CASES.md](CASES.md).
- **Concurrency:** one logical Case under the MVP one-active-case rule; contested creation/claim/assignment has one deterministic winner.
- **Settlement history:** one Case may have multiple Settlements across reopen/resolution cycles. The Case may expose a deterministic `current_settlement_id`/latest-settlement projection, but prior Settlements remain durable.

### CaseAssignment
- **Owner:** Cases.
- **Lifecycle:** `ACTIVE` → `RELEASED` | `REASSIGNED`.
- At most one active exclusive owner where the Case contract requires exclusivity.
- Assignment is not Fulfillment.

### CaseNote
- **Owner:** Cases.
- Timestamped responder note; not Follow-Up, Settlement, or Contact Attempt.

### ContactAttempt
- **Owner:** Cases.
- First-class responder contact log; not a Case Note.

### FollowUp
- **Owner:** Follow-up.
- **Lifecycle:** `SCHEDULED` → `DUE` → `COMPLETED` | `RESCHEDULED` | `OVERDUE` → `ESCALATED` | `CANCELLED`.
- **Schedule identity:** every due-time schedule has a stable `schedule_version`/equivalent identity so delayed jobs can prove they still apply before changing state.
- **Coordination attempts:** business contact-attempt count is separate from notification delivery retries and queue redelivery.
- **Resolution role:** explicit `blocking` vs `carried_forward` semantics determine whether a Follow-Up blocks Case resolution.

---

## 6. Requests, providers, resources

### ServiceRequest
- **Owner:** Requests / Dispatch.
- **Purpose:** one requested need inside a Support Case.
- **Category:** MVP `FOOD` | `TRANSPORTATION` | `SHELTER` | `PEER_SUPPORT`.
- **Lifecycle:** `CREATED` → `SUBMITTED` → `TRIAGED` → `MATCHING` → `ASSIGNED` → `ACCEPTED` → `IN_PROGRESS` → `FULFILLED` → `CONFIRMED` → `CLOSED` plus documented exceptions.
- **Current assignment projection:** deterministic from assignment/provider-attempt history; provider status is not the Service Request lifecycle.

### ServiceProvider
- **Owner:** Administration / Resources.
- **Lifecycle:** `ACTIVE` → `SUSPENDED` → `ARCHIVED`.
- A valid provider may have no API.

### ServiceOffer
- **Owner:** Resources.
- Provider offering for a category/capability; may include capacity, hours, integration modes.

### Resource
- **Owner:** Resources.
- Catalog entry with category, service name, active state, freshness verification, and optional coverage/contact/cost/capacity/integration-mode data.
- Catalog freshness is not live provider availability.

### ResourceCategory
- **Owner:** Resources.
- MVP codes: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.

---

## 7. Provider integration entities

### ProviderAdapterConfiguration
- **Owner:** Provider Router / Administration.
- Binds environment/tenant/provider/capability to an enabled adapter without making the vendor a domain type.
- Credentials remain deployment secrets, not entity fields.

### FulfillmentAttempt
- **Owner:** Fulfillment / Provider Router.
- One deliberate attempt to obtain support from one provider adapter or manual path.
- **Required:** attempt id, Service Request, tenant, capability, adapter id, integration mode, stable idempotency key, attempt status, created time.
- **Attempt status:** `PROVIDER_PENDING` | `PROVIDER_ACCEPTED` | `PROVIDER_IN_PROGRESS` | `PROVIDER_COMPLETED` | `PROVIDER_DECLINED` | `PROVIDER_CANCELLED` | `PROVIDER_FAILED` | `PROVIDER_UNKNOWN` | `MANUAL_PENDING` | `MANUAL_COMPLETED` | `MANUAL_FAILED`.
- Retry of the same logical attempt reuses idempotency identity; deliberate reroute creates a new attempt; `PROVIDER_UNKNOWN` reconciles before duplicate-risk mutation.

### ProviderOffer
- **Owner:** Provider Router / Fulfillment.
- Normalized offer shape; not Fulfillment and not canonical availability truth beyond its freshness/expiry semantics.

---

## 8. Fulfillment, referral, settlement

### ServiceFulfillment
- **Owner:** Fulfillment.
- SUAS record of acceptance/start/completion/confirmation/failure/partial/cancel.
- **Lifecycle:** `ACCEPTED` → `STARTED` → `COMPLETED` → `CONFIRMED` | `DISPUTED`, or `FAILED` | `PARTIAL` | `CANCELLED`.
- May link to the FulfillmentAttempt that supplied evidence.
- Provider completion cannot bypass SUAS confirmation/authorization semantics.
- History must remain inspectable even if a later implementation chooses a current-row projection.

### Referral
- **Owner:** Referrals.
- Directed handoff distinct from Service Request/Fulfillment.
- **Lifecycle:** `DRAFTED` → `SENT` → `ACKNOWLEDGED` → `ACCEPTED` | `DECLINED` → `COMPLETED` | `UNABLE_TO_SERVE` | `CANCELLED`.
- Send is idempotent and requires applicable Consent Grant at use time.

### Settlement
- **Owner:** Settlement.
- **Purpose:** one durable resolution-cycle record for one Support Case.
- **Required logical identity:** `settlement_id`, `support_case_id`, `tenant_id`, `resolution_cycle`, `settled_at`, responder confirmation, requested/occurred/fulfilled/unresolved summaries, remaining Follow-Up references.
- **Optional:** veteran confirmation and structured references to Service Requests, Referrals, Fulfillments, Contact Attempts.
- **Lifecycle:** created/drafted during resolution; once the Case enters `RESOLVED`, the settled record is historical business meaning and must not be silently rewritten.
- Reopening the Case does not mutate/delete the prior Settlement; a later resolution creates a new Settlement with a later resolution cycle.
- `current/latest Settlement` is a deterministic projection, not a replacement of history.
- Settlement is not a clinical outcome and not Fulfillment.

---

## 9. Notifications

### Notification
- **Owner:** Notifications.
- One logical send, not one row per retry.
- Delivery state is independent of Case/Request/Fulfillment state.

### NotificationPreference
- **Owner:** Notifications.
- Channel preference only; cannot grant consent.

---

## 10. Command idempotency and events

### CommandIdempotencyRecord
- **Owner:** Application Command / Persistence boundary.
- **Purpose:** reserve and replay the authoritative result of an unsafe logical command when [API.md](API.md) requires idempotency.
- **Required logical fields:** tenant scope, idempotency key, command/route scope, request fingerprint or equivalent misuse guard, processing/result state, authoritative result reference, created/expiry metadata as policy allows.
- Duplicate delivery with the same valid key returns/reuses the original logical outcome; conflicting payload reuse fails rather than executing a second effect.
- This record is not a Domain Event and does not replace domain uniqueness constraints.

### AuditEvent
- **Owner:** Audit/Event Layer.
- Immutable who/what/when/target record.

### DomainEvent
- **Owner:** Audit/Event Layer.
- Immutable canonical business fact using [EVENT_MODEL.md](EVENT_MODEL.md).

Provider attempt telemetry may remain Audit Events until new domain event names are explicitly accepted.

---

## 11. Pilot and feedback

### Pilot
- **Owner:** Administration.
- Current controlled pilot: Santa Clara County, approximately 25–50 veterans.
- Pilot size is operating scope, not architecture ceiling.

### PilotEnrollment
- **Owner:** Veteran Profiles / Administration.
- **Lifecycle:** `APPLIED` → `ENROLLED` → `WITHDRAWN` | `COMPLETED` | `REMOVED`.

### Feedback
- **Owner:** Administration.
- Operational/satisfaction feedback, not a clinical instrument.

---

## 12. Cross-cutting invariants

1. Every tenant-owned entity carries tenant scope.
2. Consent is evaluated at use time; provider disclosure is minimum-necessary projected.
3. Domain/authorization correctness cannot depend on one app process.
4. Provider SDK types/statuses/payloads do not become domain types.
5. ServiceProvider does not imply API capability.
6. Service Request state is independent of provider-attempt state.
7. Assignment is not Fulfillment; ProviderOffer is not Fulfillment; Referral completion is not automatically Fulfillment; Settlement is not Fulfillment.
8. External mutation retries are idempotent per FulfillmentAttempt.
9. Duplicate/out-of-order callbacks/jobs cannot silently corrupt canonical state.
10. Contested exclusive commands are atomic.
11. Domain/Audit Events remain immutable and required publication is replay-safe.
12. Current/effective projections are deterministic and reproducible from durable facts.
13. Multi-cycle Settlement history is preserved across reopen.
14. Follow-Up stale jobs cannot mutate a newer schedule.
15. API command idempotency does not rely only on process memory.
16. Mutable lists/history use bounded/paginated production access as they grow.
