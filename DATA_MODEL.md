# DATA_MODEL.md — Logical schema (SUAS v0.1)

**Related:** [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [EVENT_MODEL.md](EVENT_MODEL.md), [ARCHITECTURE.md](ARCHITECTURE.md), [CONSENT.md](CONSENT.md)

**Status:** `draft` / `0.1.0`. This is a **normalized logical schema**, not a physical migration. Types are logical. PostgreSQL is the target engine ([ARCHITECTURE.md](ARCHITECTURE.md)).

---

## 1. Conventions

| Convention | Rule |
|---|---|
| Primary key | `*_id` UUID unless a natural version key is specified |
| Foreign keys | named `*_id` matching the referenced PK |
| Tenant | `tenant_id` UUID on every tenant-owned table |
| Timestamps | `created_at`, `updated_at` on mutable tables; `occurred_at` / `computed_at` / `sent_at` as specified |
| Soft-delete | `deleted_at` nullable on mutable operational tables listed below |
| Immutable | no `updated_at` overwrite of business meaning; no `deleted_at` |
| Enums | stored as constrained text matching this stack's names exactly |

**Mutable operational tables:** User, VeteranProfile, Organization, OrganizationMembership, ResponderProfile, CheckIn (status only), TrustedContact, ConsentGrant (status/revocation fields), SupportCase, CaseAssignment, CaseNote, ContactAttempt (outcome/`completed_at` only), FollowUp, ServiceRequest, ServiceProvider, ServiceOffer, ServiceFulfillment, Resource, ResourceCategory, Referral, Notification (`delivery_status` / `attempt_count` / `last_attempt_at` / `sent_at` only), NotificationPreference, Pilot, PilotEnrollment, Feedback.

**Immutable tables:** QuestionnaireVersion (once `PUBLISHED`), Question and AnswerOption belonging to a published version, CheckInResponse (append; corrections are new rows or new Check-Ins), SupportSignal, ConsentEvent, AuditEvent, DomainEvent.

---

## 2. Identity and tenancy

### users
- PK `user_id`
- `tenant_id` (system tenant allowed for SUAS-admin)
- `email` nullable, `phone` nullable, `status` (`INVITED`|`ACTIVE`|`SUSPENDED`|`REVOKED`)
- `created_at`, `updated_at`, `deleted_at`
- Unique (email) where present; unique (phone) where present

### veteran_profiles
- PK `veteran_profile_id`
- FK `user_id` → users (unique)
- `tenant_id`, `display_name`
- `preferred_language` nullable, `preferred_channel` nullable, `county` nullable
- `created_at`, `updated_at`, `deleted_at`

### organizations
- PK `organization_id`
- `tenant_id`, `name`, `status` (`PENDING`|`ACTIVE`|`SUSPENDED`|`ARCHIVED`)
- `contact_email` nullable, `website` nullable, `counties` (text array)
- `created_at`, `updated_at`, `deleted_at`

### organization_memberships
- PK `membership_id`
- FK `organization_id`, FK `user_id`
- `role` (`RESPONDER`|`ORG_ADMIN`|`SERVICE_PROVIDER_USER`)
- `status` (`INVITED`|`ACTIVE`|`SUSPENDED`|`REVOKED`)
- `created_at`, `updated_at`, `deleted_at`
- Unique (`organization_id`, `user_id`) where not deleted

### responder_profiles
- PK `responder_profile_id`
- FK `user_id`, FK `organization_id`
- `active_for_queue` boolean
- `created_at`, `updated_at`, `deleted_at`

---

## 3. Questionnaires and check-ins

### questionnaire_versions
- PK `questionnaire_version` (text, e.g. `qv-0.1.0`)
- `status` (`DRAFT`|`PUBLISHED`|`SUPERSEDED`)
- `published_at` nullable
- `created_at`
- Immutable after `PUBLISHED`

### questions
- PK `question_id`
- FK `questionnaire_version`
- `dimension` (`sleep`|`connection`|`stress`|`basic_needs`|`coping`|`safety`| other published codes)
- `prompt`, `sort_order`, `required` boolean
- Immutable when parent version is `PUBLISHED`

### answer_options
- PK `answer_option_id`
- FK `question_id`
- `label`, `value`, `sort_order`
- Immutable when parent version is `PUBLISHED`

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
- Unique (`check_in_id`, `question_id`) for the current answer set; amendments add a new Check-In

---

## 4. Signals (immutable rows)

### support_signals
- PK `support_signal_id`
- FK `veteran_profile_id`
- FK `check_in_id` nullable
- `tenant_id`
- `level` (`GREEN`|`YELLOW`|`ORANGE`|`RED`)
- `signal_version` text
- `input_questionnaire_version` text
- `computed_at`
- `basis` (structured text/JSON of inputs used; inspectable)
- `override_of_signal_id` nullable FK → support_signals
- `override_actor_id` nullable FK → users
- `override_reason` nullable
- no `deleted_at`

---

## 5. Trusted circle and consent

### trusted_contacts
- PK `trusted_contact_id`
- FK `veteran_profile_id`
- FK `user_id` nullable
- `tenant_id`
- `relationship_label`
- `invite_email` nullable, `invite_phone` nullable
- `status` (`INVITED`|`ACCEPTED`|`SUSPENDED`|`REMOVED`|`REVOKED`)
- `created_at`, `updated_at`, `deleted_at`

### consent_grants
- PK `consent_grant_id`
- FK `veteran_profile_id`
- `tenant_id`
- `grantee_type` (`TRUSTED_CONTACT`|`RESPONDER`|`ORGANIZATION`|`SERVICE_PROVIDER`|`SYSTEM`)
- `grantee_id`
- `permission` (e.g. `can_receive`, `can_view`)
- `scope` (e.g. `YELLOW`, `ORANGE`, `RED`, `support_signal`, `checkin_answers`, `current_requests`, `location`)
- `purpose` text
- `consent_template_version` text
- `status` (`ACTIVE`|`REVOKED`|`EXPIRED`)
- `granted_at`, `revoked_at` nullable, `expires_at` nullable
- `created_at`, `updated_at`
- Historical meaning preserved via consent_events; do not reuse a revoked row as a new grant — insert a new grant

### consent_events
- PK `consent_event_id`
- FK `consent_grant_id` nullable
- `event_type` (`GRANTED`|`REVOKED`|`EXPIRED`|`DENIED`|`TEMPLATE_ACCEPTED`)
- `actor_id`, `occurred_at`, `payload` nullable
- immutable

---

## 6. Cases, notes, assignments, follow-ups

### support_cases
- PK `support_case_id`
- FK `veteran_profile_id`
- `tenant_id`
- `status` (`OPEN`|`TRIAGED`|`ASSIGNED`|`ACTIVE`|`FOLLOWUP`|`RESOLVED`|`CLOSED`)
- `priority_signal_level` nullable
- `opened_at`, `closed_at` nullable
- FK `settlement_id` nullable
- `created_at`, `updated_at`, `deleted_at` (soft-delete does not erase events)

### case_assignments
- PK `case_assignment_id`
- FK `support_case_id`, FK `responder_profile_id`
- `status` (`ACTIVE`|`RELEASED`|`REASSIGNED`)
- `assigned_at`, `released_at` nullable
- `created_at`, `updated_at`

### case_notes
- PK `case_note_id`
- FK `support_case_id`, FK `author_user_id` → users
- `body`
- `created_at`, `updated_at`, `deleted_at`
- Not a Contact Attempt. Veteran cannot read (D-015; [CASES.md](CASES.md) section 8).

### contact_attempts
- PK `contact_attempt_id`
- FK `support_case_id`
- FK `actor_id` → users
- `tenant_id`
- `at` (contact timestamp; required)
- `channel` (`EMAIL`|`SMS`|`IN_APP`|`PHONE`)
- `outcome` (`PENDING`|`REACHED`|`NO_ANSWER`|`LEFT_MESSAGE`|`DECLINED`|`UNABLE`)
- `completed_at` nullable
- `created_at`, `updated_at`
- `log-contact-attempt` inserts a row (outcome may be `PENDING`)
- `complete-contact` may set `outcome` + `completed_at` on an existing row, or insert a new row; both emit `RESPONDER_CONTACT_LOGGED`
- A Case Note is not a substitute
- Veteran cannot read (D-015)

### follow_ups
- PK `follow_up_id`
- FK `support_case_id`
- FK `service_request_id` nullable
- `tenant_id`
- `due_at`
- `responsible_type` (`RESPONDER`|`VETERAN`|`ORG_ADMIN`|`SYSTEM`)
- `responsible_id`
- `status` (`SCHEDULED`|`DUE`|`COMPLETED`|`RESCHEDULED`|`OVERDUE`|`ESCALATED`|`CANCELLED`)
- `retry_count` integer default 0
- `completed_at` nullable
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
- `name`, `status` (`ACTIVE`|`SUSPENDED`|`ARCHIVED`)
- `created_at`, `updated_at`, `deleted_at`

### service_offers
- PK `service_offer_id`
- FK `service_provider_id`
- `category`, `active` boolean
- `capacity` nullable, `hours` nullable
- `created_at`, `updated_at`, `deleted_at`

### service_fulfillments
- PK `service_fulfillment_id`
- FK `service_request_id` (unique current fulfillment; history via events)
- `status` (`ACCEPTED`|`STARTED`|`COMPLETED`|`CONFIRMED`|`DISPUTED`|`FAILED`|`PARTIAL`|`CANCELLED`)
- `accepted_at`, `started_at` nullable, `completed_at` nullable
- `veteran_confirmed_at` nullable, `responder_confirmed_at` nullable
- `failure_reason` nullable
- `created_at`, `updated_at`
- Funding columns: **not present in MVP** (`FUTURE`)

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
- `coverage_geometry` nullable (logical; type `DECISION_PENDING`)
- `hours` nullable
- `contact_method` nullable
- `referral_method` nullable
- `cost` nullable
- `capacity` nullable
- `active` boolean
- `last_verified_at` **required**
- `verification_source` **required**
- `created_at`, `updated_at`, `deleted_at`

### referrals
- PK `referral_id`
- FK `support_case_id`
- FK `service_request_id` nullable
- FK `consent_grant_id` **required**
- FK `follow_up_id` nullable
- `destination_type`, `destination_id`
- `reason`, `method`
- `status` (`DRAFTED`|`SENT`|`ACKNOWLEDGED`|`ACCEPTED`|`DECLINED`|`COMPLETED`|`UNABLE_TO_SERVE`|`CANCELLED`)
- `result_text` nullable
- `created_at`, `updated_at`, `deleted_at`

---

## 9. Notifications

### notification_preferences
- PK `notification_preference_id`
- FK `user_id`
- `channel` (`EMAIL`|`SMS`|`IN_APP`; `PUSH` reserved `FUTURE`)
- `enabled` boolean
- `created_at`, `updated_at`

### notifications
- PK `notification_id`
- `recipient_user_id` nullable, `recipient_address` nullable
- `reason`, `channel`
- `consent_basis` (grant id or documented system basis)
- `template_version`
- `delivery_status` (`QUEUED`|`SENT`|`FAILED`|`DELIVERED`|`BOUNCED`|`UNDELIVERABLE`)
- `attempt_count` integer default 0
- `last_attempt_at` nullable
- `created_at`, `sent_at` nullable
- **One row per logical send.** `delivery_status` transitions on this row.
- Each send attempt (initial and retry) appends an **immutable Audit Event**. Do not mutate prior attempt events. No `notification_attempts` child table.
- no silent delete of send records

---

## 10. Pilot, feedback

### pilots
- PK `pilot_id`
- `name`, `geography`, `target_enrollment_min`, `target_enrollment_max`
- `status`
- `created_at`, `updated_at`

### pilot_enrollments
- PK `pilot_enrollment_id`
- FK `pilot_id`, FK `veteran_profile_id`
- `status` (`APPLIED`|`ENROLLED`|`WITHDRAWN`|`COMPLETED`|`REMOVED`)
- `enrolled_at` nullable
- `created_at`, `updated_at`, `deleted_at`
- Unique (`pilot_id`, `veteran_profile_id`) where not deleted

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
- `event_type`, `aggregate_type`, `aggregate_id`
- `tenant_id`
- `actor_type`, `actor_id`
- `occurred_at`
- `schema_version`
- `payload`
- unique `event_id`
- **no updates, no deletes**

### audit_events
- PK `audit_event_id` (or reuse envelope `event_id`)
- same envelope fields as domain_events plus `action`, `target_type`, `target_id`, `ip` nullable, `user_agent` nullable
- **no updates, no deletes**

Envelope details: [EVENT_MODEL.md](EVENT_MODEL.md).

---

## 12. Indexes (logical)

- All FKs indexed.
- `(tenant_id, status)` on support_cases, service_requests, follow_ups.
- `(veteran_profile_id, computed_at desc)` on support_signals.
- `(veteran_profile_id, status)` on consent_grants.
- `(support_case_id)` on service_requests, case_notes, contact_attempts, follow_ups, referrals.
- `(support_case_id, at)` on contact_attempts.
- `(last_verified_at)` on resources.
- `(due_at, status)` on follow_ups.

---

## 13. Integrity rules

1. A ServiceRequest.support_case_id must reference a SupportCase for the same veteran/tenant.
2. A ServiceFulfillment cannot exist without a ServiceRequest.
3. A Referral requires a ConsentGrant that is `ACTIVE` at send time (historical row remains if later revoked).
4. SupportSignal.level must be one of the four labels.
5. Published questionnaire_versions cannot be updated in place.
6. Domain/audit event rows cannot be updated or deleted by application roles.
