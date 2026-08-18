# DATA_MODEL.md — Logical schema (SUAS v0.1)

**Related:** [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [EVENT_MODEL.md](EVENT_MODEL.md), [ARCHITECTURE.md](ARCHITECTURE.md), [CONSENT.md](CONSENT.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md)

**Status:** `draft` / `0.1.0`. This is a normalized logical schema, not a physical migration. PostgreSQL is the target logical engine.

---

## 1. Conventions

| Convention | Rule |
|---|---|
| Primary key | `*_id` UUID unless a natural/version key is specified |
| Foreign keys | `*_id` matching referenced PK |
| Tenant | `tenant_id` on every tenant-owned table |
| Timestamps | `created_at`, `updated_at` on mutable tables plus domain timestamps |
| Soft-delete | `deleted_at` only on mutable operational tables where allowed |
| Immutable | business meaning is append-only; no silent rewrite/delete |
| Enums | constrained text matching canonical names |

**Mutable operational tables include:** User, VeteranProfile, Organization, OrganizationMembership, ResponderProfile, CheckIn status, TrustedContact, ConsentGrant status, SupportCase, CaseAssignment, CaseNote, ContactAttempt, FollowUp, ServiceRequest, ServiceProvider, ServiceOffer, ServiceFulfillment, FulfillmentAttempt, Resource, Referral, Notification delivery fields, NotificationPreference, Pilot, PilotEnrollment, Feedback, ProviderAdapterConfiguration.

**Immutable tables include:** published QuestionnaireVersion/Question/AnswerOption, CheckInResponse history, SupportSignal, ConsentEvent, AuditEvent, DomainEvent.

---

## 2. Identity and tenancy

### users
- PK `user_id`
- `tenant_id`
- `email` nullable, `phone` nullable
- `status` (`INVITED`|`ACTIVE`|`SUSPENDED`|`REVOKED`)
- `created_at`, `updated_at`, `deleted_at`
- unique email/phone where present

### veteran_profiles
- PK `veteran_profile_id`
- FK `user_id` unique
- `tenant_id`, `display_name`
- `preferred_language` nullable, `preferred_channel` nullable, `county` nullable
- `created_at`, `updated_at`, `deleted_at`

### organizations
- PK `organization_id`
- `tenant_id`, `name`, `status` (`PENDING`|`ACTIVE`|`SUSPENDED`|`ARCHIVED`)
- `contact_email` nullable, `website` nullable, `counties` text array
- `created_at`, `updated_at`, `deleted_at`

### organization_memberships
- PK `membership_id`
- FK `organization_id`, FK `user_id`
- `role` (`RESPONDER`|`ORG_ADMIN`|`SERVICE_PROVIDER_USER`)
- `status` (`INVITED`|`ACTIVE`|`SUSPENDED`|`REVOKED`)
- `created_at`, `updated_at`, `deleted_at`
- unique (`organization_id`,`user_id`) where active/not deleted

### responder_profiles
- PK `responder_profile_id`
- FK `user_id`, FK `organization_id`
- `active_for_queue` boolean
- `created_at`, `updated_at`, `deleted_at`

---

## 3. Questionnaires and check-ins

### questionnaire_versions
- PK `questionnaire_version`
- `status` (`DRAFT`|`PUBLISHED`|`SUPERSEDED`)
- `published_at` nullable, `created_at`
- immutable after `PUBLISHED`

### questions
- PK `question_id`
- FK `questionnaire_version`
- `dimension`, `prompt`, `sort_order`, `required`
- immutable under published parent

### answer_options
- PK `answer_option_id`
- FK `question_id`
- `label`, `value`, `sort_order`
- immutable under published parent

### check_ins
- PK `check_in_id`
- FK `veteran_profile_id`, FK `questionnaire_version`
- `tenant_id`
- `status` (`STARTED`|`IN_PROGRESS`|`COMPLETED`|`ABANDONED`|`INCOMPLETE`)
- `started_at`, `completed_at` nullable, `abandoned_at` nullable
- `created_at`, `updated_at`, `deleted_at`

### check_in_responses
- PK `check_in_response_id`
- FK `check_in_id`, FK `question_id`
- `answer_option_id` nullable, `free_text` nullable
- `answered_at`

---

## 4. Signals

### support_signals
- PK `support_signal_id`
- FK `veteran_profile_id`
- FK `check_in_id` nullable
- `tenant_id`
- `level` (`GREEN`|`YELLOW`|`ORANGE`|`RED`)
- `signal_version`, `input_questionnaire_version`, `computed_at`, `basis`
- `override_of_signal_id`, `override_actor_id`, `override_reason` nullable
- immutable row

---

## 5. Trusted circle and consent

### trusted_contacts
- PK `trusted_contact_id`
- FK `veteran_profile_id`
- FK `user_id` nullable
- `tenant_id`, `relationship_label`
- `invite_email` nullable, `invite_phone` nullable
- `status` (`INVITED`|`ACCEPTED`|`SUSPENDED`|`REMOVED`|`REVOKED`)
- `created_at`, `updated_at`, `deleted_at`

### consent_grants
- PK `consent_grant_id`
- FK `veteran_profile_id`
- `tenant_id`
- `grantee_type` (`TRUSTED_CONTACT`|`RESPONDER`|`ORGANIZATION`|`SERVICE_PROVIDER`|`SYSTEM`)
- `grantee_id`, `permission`, `scope`, `purpose`, `consent_template_version`
- `status` (`ACTIVE`|`REVOKED`|`EXPIRED`)
- `granted_at`, `revoked_at` nullable, `expires_at` nullable
- `created_at`, `updated_at`

### consent_events
- PK `consent_event_id`
- FK `consent_grant_id` nullable
- `event_type` (`GRANTED`|`REVOKED`|`EXPIRED`|`DENIED`|`TEMPLATE_ACCEPTED`)
- `actor_id`, `occurred_at`, `payload` nullable
- immutable

---

## 6. Cases, assignments, notes, contact, follow-up

### support_cases
- PK `support_case_id`
- FK `veteran_profile_id`
- `tenant_id`
- `status` (`OPEN`|`TRIAGED`|`ASSIGNED`|`ACTIVE`|`FOLLOWUP`|`RESOLVED`|`CLOSED`)
- `priority_signal_level` nullable
- `opened_at`, `closed_at` nullable
- FK `settlement_id` nullable
- `created_at`, `updated_at`, `deleted_at`

### case_assignments
- PK `case_assignment_id`
- FK `support_case_id`, FK `responder_profile_id`
- `status` (`ACTIVE`|`RELEASED`|`REASSIGNED`)
- `assigned_at`, `released_at` nullable
- `created_at`, `updated_at`
- at most one active owner where the Case contract requires exclusive claim; enforced transactionally/constraint-backed

### case_notes
- PK `case_note_id`
- FK `support_case_id`, FK `author_user_id`
- `body`
- `created_at`, `updated_at`, `deleted_at`
- not a Contact Attempt

### contact_attempts
- PK `contact_attempt_id`
- FK `support_case_id`, FK `actor_id`
- `tenant_id`
- `at`, `channel` (`EMAIL`|`SMS`|`IN_APP`|`PHONE`)
- `outcome` (`PENDING`|`REACHED`|`NO_ANSWER`|`LEFT_MESSAGE`|`DECLINED`|`UNABLE`)
- `completed_at` nullable
- `created_at`, `updated_at`

### follow_ups
- PK `follow_up_id`
- FK `support_case_id`
- FK `service_request_id` nullable
- `tenant_id`, `due_at`
- `responsible_type` (`RESPONDER`|`VETERAN`|`ORG_ADMIN`|`SYSTEM`)
- `responsible_id`
- `status` (`SCHEDULED`|`DUE`|`COMPLETED`|`RESCHEDULED`|`OVERDUE`|`ESCALATED`|`CANCELLED`)
- `retry_count` default 0, `completed_at` nullable
- `created_at`, `updated_at`, `deleted_at`

---

## 7. Requests, providers, fulfillment

### service_requests
- PK `service_request_id`
- FK `support_case_id`
- `tenant_id`
- `category` (`FOOD`|`TRANSPORTATION`|`SHELTER`|`PEER_SUPPORT` in MVP)
- `status` (`CREATED`|`SUBMITTED`|`TRIAGED`|`MATCHING`|`ASSIGNED`|`ACCEPTED`|`IN_PROGRESS`|`FULFILLED`|`CONFIRMED`|`CLOSED`|`CANCELLED`|`DECLINED`|`EXPIRED`|`UNFULFILLABLE`|`ESCALATED`)
- `details` nullable
- `created_at`, `updated_at`, `deleted_at`

### service_providers
- PK `service_provider_id`
- FK `organization_id` nullable
- `tenant_id`
- `name`, `status` (`ACTIVE`|`SUSPENDED`|`ARCHIVED`)
- `created_at`, `updated_at`, `deleted_at`

### service_offers
- PK `service_offer_id`
- FK `service_provider_id`
- `category`, `active`
- `capacity` nullable, `hours` nullable
- `integration_modes` constrained text array from (`API`,`WEBHOOK`,`DEEP_LINK`,`PHONE`,`EMAIL`,`MANUAL_COORDINATION`,`NONE`)
- `created_at`, `updated_at`, `deleted_at`

### provider_adapter_configurations
- PK `provider_adapter_configuration_id`
- `tenant_id`
- FK `service_provider_id` nullable
- `adapter_id` opaque SUAS identifier; vendor secret/config remains deployment secret
- `capability` (`TRANSPORTATION_FULFILLMENT`|`TEMPORARY_SHELTER_FULFILLMENT`|`FOOD_SUPPORT_FULFILLMENT`|`PEER_SUPPORT_FULFILLMENT` or accepted future capability)
- `integration_mode`
- `enabled` boolean
- `coverage_scope` nullable structured configuration
- `priority` nullable integer
- `created_at`, `updated_at`, `deleted_at`
- MUST NOT store API credentials/secrets

### fulfillment_attempts
- PK `fulfillment_attempt_id`
- FK `service_request_id`
- `tenant_id`
- `capability`
- `provider_adapter_id` (logical adapter reference; may be `MANUAL`)
- FK `service_provider_id` nullable
- `integration_mode`
- `idempotency_key` unique within provider/capability scope as required
- `status` (`PROVIDER_PENDING`|`PROVIDER_ACCEPTED`|`PROVIDER_IN_PROGRESS`|`PROVIDER_COMPLETED`|`PROVIDER_DECLINED`|`PROVIDER_CANCELLED`|`PROVIDER_FAILED`|`PROVIDER_UNKNOWN`|`MANUAL_PENDING`|`MANUAL_COMPLETED`|`MANUAL_FAILED`)
- `external_reference` nullable
- `last_provider_status` nullable adapter-normalized text snapshot only
- `last_checked_at` nullable
- `failure_reason` nullable
- `created_at`, `updated_at`
- one logical provider mutation attempt keeps one stable idempotency identity; deliberate reroute creates a new row

### service_fulfillments
- PK `service_fulfillment_id`
- FK `service_request_id` (unique current fulfillment unless later spec explicitly changes history model)
- `tenant_id`
- FK `fulfillment_attempt_id` nullable (the attempt that produced current fulfillment evidence; manual/internal allowed)
- `status` (`ACCEPTED`|`STARTED`|`COMPLETED`|`CONFIRMED`|`DISPUTED`|`FAILED`|`PARTIAL`|`CANCELLED`)
- `accepted_at`, `started_at` nullable, `completed_at` nullable
- `veteran_confirmed_at` nullable, `responder_confirmed_at` nullable
- `failure_reason` nullable
- `created_at`, `updated_at`
- funding/payment columns absent in MVP

---

## 8. Resources and referrals

### resource_categories
- PK `category_code`
- `label`, `mvp` boolean
- `created_at`

### resources
- PK `resource_id`
- FK `organization_id`
- FK `category` → resource_categories
- `tenant_id`
- `service_name`
- `eligibility` nullable
- `counties` text array
- `coverage_geometry` nullable; physical type D-014/implementation decision
- `hours`, `contact_method`, `referral_method`, `cost`, `capacity` nullable
- `integration_modes` constrained text array optional
- `active`
- `last_verified_at` required, `verification_source` required
- `created_at`, `updated_at`, `deleted_at`

### referrals
- PK `referral_id`
- FK `support_case_id`
- FK `service_request_id` nullable
- FK `consent_grant_id` required
- FK `follow_up_id` nullable
- `destination_type`, `destination_id`, `reason`, `method`
- `status` (`DRAFTED`|`SENT`|`ACKNOWLEDGED`|`ACCEPTED`|`DECLINED`|`COMPLETED`|`UNABLE_TO_SERVE`|`CANCELLED`)
- `result_text` nullable
- `created_at`, `updated_at`, `deleted_at`

---

## 9. Notifications

### notification_preferences
- PK `notification_preference_id`
- FK `user_id`
- `channel` (`EMAIL`|`SMS`|`IN_APP`; `PUSH` future)
- `enabled`
- `created_at`, `updated_at`

### notifications
- PK `notification_id`
- `tenant_id`
- `recipient_user_id` nullable, `recipient_address` nullable
- `reason`, `channel`, `consent_basis`, `template_version`
- `delivery_status` (`QUEUED`|`SENT`|`FAILED`|`DELIVERED`|`BOUNCED`|`UNDELIVERABLE`)
- `attempt_count` default 0, `last_attempt_at` nullable
- `created_at`, `sent_at` nullable
- one row per logical send; each attempt appends immutable Audit Event

---

## 10. Pilot and feedback

### pilots
- PK `pilot_id`
- `name`, `geography`, `target_enrollment_min`, `target_enrollment_max`, `status`
- `created_at`, `updated_at`

### pilot_enrollments
- PK `pilot_enrollment_id`
- FK `pilot_id`, FK `veteran_profile_id`
- `status` (`APPLIED`|`ENROLLED`|`WITHDRAWN`|`COMPLETED`|`REMOVED`)
- `enrolled_at` nullable
- `created_at`, `updated_at`, `deleted_at`

### feedback
- PK `feedback_id`
- FK `author_user_id`
- FK `support_case_id` nullable
- `body` nullable, structured score fields nullable
- `created_at`, `updated_at`, `deleted_at`

---

## 11. Immutable event stores

### domain_events
- PK `event_id`
- `event_type`, `aggregate_type`, `aggregate_id`, `tenant_id`
- `actor_type`, `actor_id`, `occurred_at`, `schema_version`, `payload`
- immutable; unique `event_id`

### audit_events
- PK `audit_event_id`
- event envelope plus `action`, `target_type`, `target_id`, `ip` nullable, `user_agent` nullable
- immutable

---

## 12. Logical indexes / access paths

At minimum:

- all FKs indexed;
- `(tenant_id,status)` on support_cases, service_requests, follow_ups, fulfillment_attempts;
- `(tenant_id,created_at desc)` on high-volume operational collections as query needs require;
- `(veteran_profile_id,computed_at desc)` on support_signals;
- `(veteran_profile_id,status)` on consent_grants;
- `(support_case_id)` on service_requests, case_notes, contact_attempts, follow_ups, referrals;
- `(support_case_id,at)` on contact_attempts;
- `(last_verified_at)` on resources;
- `(due_at,status)` on follow_ups;
- `(service_request_id,created_at)` on fulfillment_attempts;
- `(provider_adapter_id,status,updated_at)` or equivalent on fulfillment_attempts where adapter reconciliation uses it;
- `(tenant_id,delivery_status,created_at)` on notifications for worker/ops queries;
- event/audit timestamp + tenant/aggregate access paths appropriate to retention/volume.

Exact physical indexes are validated by representative query plans/load tests; do not add speculative indexes that harm writes without evidence.

---

## 13. Scalability constraints

1. Unbounded list APIs must use bounded/paginated queries.
2. Normal responder/veteran screens must not require full history table scans.
3. Worker pickup queries for notifications/follow-ups/provider attempts need indexable status/due-time predicates.
4. Idempotency lookup must be indexed/unique enough to avoid duplicate external side effects under concurrency.
5. Connection pooling is required in production architecture.
6. Audit/event growth must have an accepted retention/archive plan once D-007 closes.
7. Partitioning/read replicas/sharding are evidence-driven later options, not MVP requirements.

---

## 14. Integrity rules

1. A ServiceRequest references a SupportCase in the same tenant/veteran context.
2. A ServiceFulfillment cannot exist without a ServiceRequest.
3. A FulfillmentAttempt cannot exist without a ServiceRequest.
4. FulfillmentAttempt idempotency identity cannot silently change after an external mutation has been attempted.
5. `PROVIDER_UNKNOWN` cannot be treated as a safe signal to issue a duplicate external mutation without reconciliation/new deliberate attempt.
6. Provider-specific status does not directly mutate canonical ServiceRequest state outside documented commands.
7. A Referral requires an ACTIVE ConsentGrant at send time; historical row remains if later revoked.
8. Provider disclosure requires applicable Consent/minimum-necessary projection at call time.
9. Published questionnaire versions are immutable.
10. Domain/Audit Events cannot be updated/deleted by application roles.
11. Provider credentials/secrets are not stored in provider/resource tables.
12. Case claim/assignment constraints must support one deterministic winner under concurrent exclusive claims.

---

## 15. Non-goals

- Physical migration syntax
- Vendor-specific provider schemas
- Payment/billing columns in MVP
- Database sharding before evidence
- Storing raw provider webhook payloads indefinitely
- Storing provider API secrets in the application relational domain model
