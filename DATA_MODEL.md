# DATA_MODEL.md — Logical schema (SUAS v0.1)

**Related:** [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [EVENT_MODEL.md](EVENT_MODEL.md), [ARCHITECTURE.md](ARCHITECTURE.md), [CONSENT.md](CONSENT.md), [CHECKINS.md](CHECKINS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [SETTLEMENT.md](SETTLEMENT.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md)

**Status:** `draft` / `0.1.0`. Normalized logical schema only; physical migrations remain implementation work after release. SPEC-006 is dependency-blocked; this is draft reconciliation, not acceptance.

---

## 1. Conventions

| Convention | Rule |
|---|---|
| Primary key | `*_id` UUID unless a natural/version key is specified |
| Foreign keys | `*_id` matching referenced PK |
| Tenant | `tenant_id` on tenant-owned rows |
| Timestamps | server-authoritative domain timestamps plus `created_at`/`updated_at` on mutable rows |
| Soft-delete | only where the domain contract permits |
| Immutable | historical business meaning is never silently rewritten |
| Current projection | deterministic pointer/query over durable history; insertion order alone is not authority |

---

## 2. Identity and organization

### users
- PK `user_id`; `tenant_id`; nullable email/phone; status `INVITED|ACTIVE|SUSPENDED|REVOKED`; timestamps/deleted_at.

### veteran_profiles
- PK `veteran_profile_id`; FK `user_id` unique; `tenant_id`, `display_name`; optional language/channel/county; timestamps.

### organizations
- PK `organization_id`; `tenant_id`, name, status `PENDING|ACTIVE|SUSPENDED|ARCHIVED`; optional contact/website/counties.

### organization_memberships
- PK `membership_id`; FKs organization/user; role `RESPONDER|ORG_ADMIN|SERVICE_PROVIDER_USER`; lifecycle status; unique active membership per org/user.

### responder_profiles
- PK `responder_profile_id`; FKs user/organization; `active_for_queue`; timestamps.

---

## 3. Questionnaire and Check-In

### questionnaire_versions
- PK `questionnaire_version`; status `DRAFT|PUBLISHED|SUPERSEDED`; `published_at`; immutable after publish; publication visible atomically as a complete version.

### questions / answer_options
- Version-bound immutable content once parent is published.

### check_ins
- PK `check_in_id`; FKs veteran/questionnaire; `tenant_id`; status `STARTED|IN_PROGRESS|COMPLETED|ABANDONED|INCOMPLETE`; lifecycle timestamps.
- Logical completion is idempotent.

### check_in_responses
- PK `check_in_response_id`; FKs Check-In/question; answer/free-text; `answered_at`.
- Completed response history is append-preserved.

---

## 4. Support Signals

### support_signals
- PK `support_signal_id`; FK veteran; nullable FK Check-In; `tenant_id`.
- `computation_kind` = `PRIMARY|OVERRIDE`.
- `computation_key` stable opaque logical identity.
- `source_type` = `CHECK_IN|EXPLICIT_NEED`; stable `source_id` required for non-Check-In sources.
- `level` = `GREEN|YELLOW|ORANGE|RED`.
- `signal_version`, `input_questionnaire_version`, `computed_at`, structured `basis`.
- override linkage/actor/reason nullable.
- immutable.
- Primary uniqueness: one authoritative logical primary for source + signal version + questionnaire version, enforced by computation identity/constraint.
- Override rows use distinct identity and link to the prior signal.

### effective signal projection
Must be deterministic and reproducible from durable SupportSignal history. The implementation may use a current-pointer column/table or a deterministic query, but the rule must:
1. prefer the accepted latest applicable override/primary semantics from [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md);
2. never rely on insertion order alone;
3. retain historical rows;
4. permit efficient read of the current effective signal.

---

## 5. Consent / Trusted Circle

### trusted_contacts
- PK; veteran/user links; `tenant_id`; relationship/invite identifiers; lifecycle `INVITED|ACCEPTED|SUSPENDED|REMOVED|REVOKED`.

### consent_grants
- PK; veteran; `tenant_id`; grantee type/id; permission, scope, purpose, template version; `ACTIVE|REVOKED|EXPIRED`; timestamps.

### consent_events
- immutable grant/revoke/expire/deny/template-accept history.

---

## 6. Cases, assignments, contact, Follow-Up

### support_cases
- PK `support_case_id`; FK veteran; `tenant_id`; status `OPEN|TRIAGED|ASSIGNED|ACTIVE|FOLLOWUP|RESOLVED|CLOSED`; optional priority signal; lifecycle timestamps.
- `current_settlement_id` nullable deterministic convenience pointer only; not ownership of all Settlement history.
- Under the MVP one-active-case rule, physical constraints/transaction logic must prevent two simultaneous logical active cases for the same veteran where the contract requires exclusivity.

### case_assignments
- PK; FKs case/responder; status `ACTIVE|RELEASED|REASSIGNED`; timestamps.
- At most one active exclusive assignment per Case where required.

### case_notes / contact_attempts
- First-class separate tables; contact attempt includes tenant, actor, `at`, channel, outcome, completion timestamp.

### follow_ups
- PK `follow_up_id`; FK case; nullable FK request; `tenant_id`.
- `due_at`; `schedule_version` integer/opaque monotonic identity; responsible type/id.
- status `SCHEDULED|DUE|COMPLETED|RESCHEDULED|OVERDUE|ESCALATED|CANCELLED`.
- `coordination_attempt_count` default 0; this excludes notification retries/job redelivery.
- `resolution_disposition` = `BLOCKING|CARRIED_FORWARD` while unresolved.
- completion timestamps/reason fields as required by domain spec.
- Durable due/overdue jobs must include/compare `schedule_version` before state mutation; stale jobs no-op/audit.

---

## 7. Requests / providers / Fulfillment

### service_requests
- PK; FK case; `tenant_id`; category; canonical request status; details; timestamps.
- Current assignment/provider presentation is deterministic from accepted assignment/FulfillmentAttempt history; do not infer from provider callback order.

### service_providers / service_offers / resources
- Provider/resource catalog entities with tenant/org linkage, canonical category, integration modes, freshness metadata, and no embedded credentials.

### provider_adapter_configurations
- PK; `tenant_id`; optional provider; opaque `adapter_id`; capability; integration mode; enabled; coverage/priority config; timestamps.
- No secrets/API credentials.

### fulfillment_attempts
- PK `fulfillment_attempt_id`; FK request; `tenant_id`; capability; adapter id; optional provider; integration mode; stable `idempotency_key`; normalized attempt status; optional external ref/last provider status/check time/failure reason; timestamps.
- Same logical external attempt reuses idempotency identity; deliberate reroute creates a new attempt.

### service_fulfillments
- PK `service_fulfillment_id`; FK request; `tenant_id`; nullable FK fulfillment attempt.
- status `ACCEPTED|STARTED|COMPLETED|CONFIRMED|DISPUTED|FAILED|PARTIAL|CANCELLED`.
- fulfillment/confirmation timestamps and reason fields.
- History must remain inspectable. If a single mutable current fulfillment row is used, all meaningful prior transitions remain represented in immutable events; a later spec may instead choose explicit fulfillment-version rows.

---

## 8. Referrals and Settlement

### referrals
- PK; FK case; nullable FK request; required consent grant; nullable Follow-Up link; destination/reason/method; lifecycle state/result; timestamps.
- Logical send uses persistent command idempotency so retry does not create a second Referral/send effect.

### settlements
- PK `settlement_id`.
- FK `support_case_id`; `tenant_id`.
- `resolution_cycle` positive integer/monotonic case-local identity.
- required structured/requested summary, occurred summary, fulfilled summary, unresolved summary.
- responder confirmation actor/time required; veteran confirmation optional.
- `settled_at`; remaining Follow-Up references/summary.
- optional structured references to Service Requests, Referrals, Fulfillments, Contact Attempts.
- unique (`support_case_id`,`resolution_cycle`).
- Once used to enter Case `RESOLVED`, historical business meaning is immutable; corrections use amendment/new cycle semantics specified later, not silent overwrite.
- Reopen preserves all prior settlements; next resolution inserts a new cycle.

**Latest/current Settlement projection:** highest valid accepted resolution cycle or explicit current pointer that resolves to the same deterministic result. `support_cases.current_settlement_id` may cache this projection but cannot erase history.

---

## 9. Notifications

### notification_preferences
- user + channel + enabled state.

### notifications
- one row per logical send; tenant/recipient/reason/channel/consent basis/template; delivery status; attempt count/timestamps.
- Notification transport retries are independent of Follow-Up coordination attempt count.

---

## 10. Command idempotency

### command_idempotency_records
Logical persistence for unsafe commands that accept `Idempotency-Key`.

Required fields:
- PK `command_idempotency_record_id`;
- `tenant_id`;
- `idempotency_key`;
- `command_scope` (route/command name + relevant actor/aggregate scope);
- `request_fingerprint` or equivalent canonical payload hash;
- state `RESERVED|COMPLETED|FAILED_RETRYABLE|FAILED_FINAL`;
- `result_status_code`/authoritative result reference or bounded stored response needed for replay;
- optional linked aggregate/event ids;
- `created_at`, `completed_at`, optional expiry per accepted retention/security policy.

Constraints:
1. unique logical key within command scope/tenant;
2. same key + same request returns/reuses original logical outcome;
3. same key + conflicting request is rejected;
4. record does not replace domain uniqueness constraints or FulfillmentAttempt idempotency.

---

## 11. Immutable event stores

### domain_events
- PK `event_id`; event type/aggregate/tenant/actor/time/schema/payload.
- separate conditional `idempotency_key`, correlation id, causation event id, request id.
- immutable; unique event id; producing-domain idempotency scope prevents duplicate logical event/effect.

### audit_events
- immutable audit identity + common envelope/correlation semantics + action/target/request metadata.

### outbox/equivalent
Physical outbox may be used to couple committed domain state and required event publication. It is an implementation mechanism, not a business entity, but must provide replay-safe publication semantics from [EVENT_MODEL.md](EVENT_MODEL.md).

---

## 12. Logical indexes / constraints

At minimum, implementation design must provide efficient/constraint-backed access for:
- all FKs;
- tenant + status on Cases/Requests/Follow-Ups/FulfillmentAttempts;
- veteran + effective/current Support Signal projection and signal computation identity;
- one-active Case and active CaseAssignment uniqueness where applicable;
- case + resolution cycle and current Settlement projection;
- Follow-Up due/status/schedule-version pickup;
- Service Request + FulfillmentAttempt chronology/current attempt;
- provider adapter + attempt status/check time for reconciliation;
- notification delivery worker queries;
- command idempotency lookup;
- event/audit tenant/aggregate/time/correlation/idempotency queries.

Exact physical indexes/partial unique constraints are implementation-specific but must prove these invariants under concurrency and representative load.

---

## 13. Integrity rules

1. Tenant consistency across Case/Request/Fulfillment/Settlement relationships.
2. Published questionnaire versions immutable and atomically visible.
3. Primary Support Signal unique by logical computation identity; duplicate job delivery reuses settled result.
4. Effective Support Signal deterministic from durable facts.
5. One-active Case/assignment semantics have deterministic winner where required.
6. Provider status never silently becomes canonical Request/Fulfillment state.
7. FulfillmentAttempt idempotency cannot change after external mutation starts.
8. `PROVIDER_UNKNOWN` reconciles before duplicate-risk retry.
9. Referral/provider disclosure requires use-time consent/minimum necessary data.
10. Follow-Up stale schedule jobs cannot mutate newer schedule state.
11. Notification retry count is not Follow-Up coordination attempt count.
12. A Case can have multiple Settlements over reopen cycles; prior Settlements are preserved.
13. `current_settlement_id`, if used, must reference the deterministic latest/current valid Settlement.
14. Required Domain Event publication cannot be permanently lost after domain commit.
15. Command idempotency persists across process restart and horizontal instances.
16. Provider secrets never live in domain tables.

---

## 14. Non-goals

- physical migration syntax;
- vendor-specific provider schemas;
- payment/billing columns in MVP;
- database sharding before evidence;
- raw provider webhook retention as business data;
- provider API secrets in domain tables;
- event IDs as command idempotency substitutes;
- insertion-order current projections;
- one mutable Settlement row that destroys prior resolution cycles.
