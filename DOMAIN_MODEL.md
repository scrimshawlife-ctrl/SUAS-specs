# DOMAIN_MODEL.md — Entities (SUAS v0.1)

**Related:** [DATA_MODEL.md](DATA_MODEL.md), [GLOSSARY.md](GLOSSARY.md), [EVENT_MODEL.md](EVENT_MODEL.md), [PRIVACY.md](PRIVACY.md), [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md)

**Status:** `draft` / `0.1.0`. Retention durations are `DECISION_PENDING` (D-007) unless a row says otherwise.

Field sensitivity: `required` / `optional` / `sensitive`. `sensitive` means highly restricted, logged on access, never placed in ordinary logs.

Deletion: operational entities may be soft-deleted. Domain Events and Audit Events are immutable and are not deleted by ordinary operations.

---

## Conventions

- **Owner:** module that may write the entity (see [ARCHITECTURE.md](ARCHITECTURE.md)).
- **Authz:** who may read/write beyond the owner module.
- **Lifecycle:** states or created→active→terminal.
- **Retention:** `DECISION_PENDING` unless specified. Soft-delete does not satisfy a legal deletion request by itself; see [PRIVACY.md](PRIVACY.md).

---

## User

- **Purpose:** Login identity shared by Veteran, Responder, Organization Administrator, Trusted Contact (if enrolled), SUAS System Administrator, and Service Provider users.
- **Owner:** Administration / Auth.
- **Lifecycle:** `INVITED` → `ACTIVE` → `SUSPENDED` → `REVOKED`. Revoked users cannot authenticate or act. Historical actor_id on events remains.
- **Required:** `user_id`, `tenant_id` (may be system-tenant for SUAS-admin), `status`, `created_at`.
- **Optional:** `email`, `phone` (at least one required for veterans; responders require email).
- **Sensitive:** `email`, `phone`.
- **Authz:** self (limited); org-admin for org members; SUAS-admin audited.
- **Deletion:** soft-delete / revoke. Sessions invalidated. See [AUTH.md](AUTH.md).

## VeteranProfile

- **Purpose:** Veteran-specific profile bound to one User.
- **Owner:** Veteran Profiles.
- **Lifecycle:** created at enrollment; active while enrolled; deactivated on pilot exit without deleting history.
- **Required:** `veteran_profile_id`, `user_id`, `tenant_id`, `display_name`, `created_at`.
- **Optional:** preferred language, preferred contact channel, county of residence (pilot default Santa Clara), notes the veteran chose to share.
- **Sensitive:** all free-text; contact preferences; any location field.
- **Authz:** veteran; assigned responder (need-to-know); SUAS-admin audited. Trusted Contacts: only fields covered by Consent Grants.
- **Non-goals:** SSN, DD-214, medical history, diagnoses — do not collect. See [PRIVACY.md](PRIVACY.md).
- **Deletion:** deactivate; retain per D-007.

## Organization

- **Purpose:** Participating entity. Placeholder ids `PARTNER_ORG_001` … until D-008.
- **Owner:** Administration.
- **Lifecycle:** `PENDING` → `ACTIVE` → `SUSPENDED` → `ARCHIVED`.
- **Required:** `organization_id`, `tenant_id` (often equals organization_id for org tenants), `name`, `status`.
- **Optional:** contact email, website, county list.
- **Sensitive:** none beyond ordinary org contact.
- **Authz:** org-admin of that org; SUAS-admin; responders of that org (read).

## OrganizationMembership

- **Purpose:** Binds a User to an Organization with a role (`RESPONDER`, `ORG_ADMIN`, `SERVICE_PROVIDER_USER`).
- **Owner:** Administration.
- **Lifecycle:** `INVITED` → `ACTIVE` → `SUSPENDED` → `REVOKED`.
- **Required:** `membership_id`, `organization_id`, `user_id`, `role`, `status`.
- **Authz:** org-admin of that org; SUAS-admin.
- **Revoked-user behavior:** membership revocation immediately removes authorization even if the User is still `ACTIVE` globally.

## ResponderProfile

- **Purpose:** Responder-specific fields (capacity flag, queue eligibility).
- **Owner:** Administration.
- **Lifecycle:** mirrors membership.
- **Required:** `responder_profile_id`, `user_id`, `organization_id`.
- **Optional:** display name, active-for-queue boolean, coverage notes (`DECISION_PENDING` actual hours — D-009).
- **Authz:** self, org-admin, SUAS-admin.
- **Non-goal:** clinical credential store.

## QuestionnaireVersion

- **Purpose:** Immutable published Check-In questionnaire.
- **Owner:** Check-ins / Administration (publish).
- **Lifecycle:** `DRAFT` → `PUBLISHED` → `SUPERSEDED`. Published rows are immutable.
- **Required:** `questionnaire_version`, `status`, `published_at` (when published).
- **Authz:** SUAS-admin writes; veterans receive the current published version; historical check-ins keep their version.
- **Spec:** [CHECKINS.md](CHECKINS.md).

## CheckIn

- **Purpose:** One questionnaire attempt by a Veteran.
- **Owner:** Check-ins.
- **Lifecycle:** `STARTED` → `IN_PROGRESS` → `COMPLETED` | `ABANDONED` | `INCOMPLETE`.
- **Required:** `check_in_id`, `veteran_profile_id`, `questionnaire_version`, `status`, `started_at`.
- **Optional:** `completed_at`, `abandoned_at`.
- **Sensitive:** yes (answers live on CheckInResponse).
- **Authz:** veteran; others only via Consent Grant or documented case-assignment policy.
- **Edits:** completed check-ins are not silently rewritten. A correction creates a new Check-In or a documented amendment event. See [CHECKINS.md](CHECKINS.md).

## CheckInResponse

- **Purpose:** One answer to one question on a Check-In.
- **Owner:** Check-ins.
- **Required:** `check_in_response_id`, `check_in_id`, `question_id`, `answered_at`.
- **Optional:** `answer_option_id`, free-text (`sensitive`).
- **Authz:** same as CheckIn.

## SupportSignal

- **Purpose:** Deterministic coordination label for a Check-In (or explicit need).
- **Owner:** Support Signals.
- **Lifecycle:** created immutable. Override writes a **new** row linked to the prior, never mutates the original.
- **Required:** `support_signal_id`, `veteran_profile_id`, `level` (`GREEN`|`YELLOW`|`ORANGE`|`RED`), `signal_version`, `input_questionnaire_version`, `computed_at`, `basis`.
- **Optional:** `check_in_id`, `override_of_signal_id`, `override_actor_id`, `override_reason`.
- **Sensitive:** level + basis.
- **Authz:** veteran; others via Consent Grant (`can_view` / `support_signal`) or assigned responder per [CONSENT.md](CONSENT.md).
- **Spec:** [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md).

## TrustedContact

- **Purpose:** One person in a Veteran's Trusted Circle.
- **Owner:** Trusted Circle.
- **Lifecycle:** `INVITED` → `ACCEPTED` → `SUSPENDED` | `REMOVED` | `REVOKED`.
- **Required:** `trusted_contact_id`, `veteran_profile_id`, `status`, `relationship_label`.
- **Optional:** `user_id` (if the contact has a User), email/phone for invite.
- **Sensitive:** contact identifiers, relationship.
- **Authz:** veteran; the contact (own row); responders do not list the circle without a grant.
- **Spec:** [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md).

## ConsentGrant

- **Purpose:** First-class permission. Not a boolean.
- **Owner:** Consent.
- **Lifecycle:** `ACTIVE` → `REVOKED` | `EXPIRED`. Revocation stops future use.
- **Required:** `consent_grant_id`, `veteran_profile_id`, `grantee_type`, `grantee_id`, `permission`, `scope`, `purpose`, `consent_template_version`, `granted_at`, `status`.
- **Optional:** `expires_at`.
- **Sensitive:** yes.
- **Authz:** veteran; system evaluate; SUAS-admin audit read.
- **Spec:** [CONSENT.md](CONSENT.md).

## ConsentEvent

- **Purpose:** Immutable history of grant, revoke, expire, deny.
- **Owner:** Consent.
- **Lifecycle:** append-only.
- **Required:** `consent_event_id`, `consent_grant_id` (nullable for deny-without-grant), `event_type`, `occurred_at`, `actor_id`.
- **Authz:** veteran (own), SUAS-admin.
- **Deletion:** not deleted in ordinary operations.

## SupportCase

- **Purpose:** Coordination around a Veteran.
- **Owner:** Cases.
- **Lifecycle:** `OPEN` → `TRIAGED` → `ASSIGNED` → `ACTIVE` → `FOLLOWUP` → `RESOLVED` → `CLOSED`. Documented skips/returns in [CASES.md](CASES.md). Closure does not delete history.
- **Required:** `support_case_id`, `veteran_profile_id`, `tenant_id`, `status`, `opened_at`.
- **Optional:** `priority_signal_level`, `closed_at`, `settlement_id`.
- **Sensitive:** yes.
- **Authz:** assigned responder; org queue per CASES; veteran (limited); SUAS-admin audited.

## CaseAssignment

- **Purpose:** Binding of a Responder to a Support Case. Assignment ≠ Fulfillment.
- **Owner:** Cases.
- **Lifecycle:** `ACTIVE` → `RELEASED` | `REASSIGNED`.
- **Required:** `case_assignment_id`, `support_case_id`, `responder_profile_id`, `assigned_at`, `status`.
- **Authz:** same as SupportCase.

## CaseNote

- **Purpose:** Timestamped note. Not a Follow-Up, not a Settlement, not a transition.
- **Owner:** Cases.
- **Lifecycle:** created; may be amended via a new note (preferred) or an amendment record. Do not silently rewrite.
- **Required:** `case_note_id`, `support_case_id`, `author_user_id`, `body`, `created_at`.
- **Sensitive:** body (malicious-content risk; see [SECURITY.md](SECURITY.md)).
- **Authz:** assigned responder; org-admin of owning org (read); SUAS-admin audited. Veteran visibility is `DECISION_PENDING` and must not default to full note access.

## FollowUp

- **Purpose:** First-class follow-up work item.
- **Owner:** Follow-up.
- **Lifecycle:** `SCHEDULED` → `DUE` → `COMPLETED` | `RESCHEDULED` | `OVERDUE` → `ESCALATED` | `CANCELLED`.
- **Required:** `follow_up_id`, `support_case_id`, `due_at`, `responsible_type`, `responsible_id`, `status`.
- **Optional:** `service_request_id`, `retry_count`.
- **Authz:** responsible party; assigned responder; veteran when addressed to them.
- **Spec:** [FOLLOWUP.md](FOLLOWUP.md).

## ServiceRequest

- **Purpose:** A specific requested need. One Support Case may contain many.
- **Owner:** Requests / Dispatch.
- **Lifecycle:** `CREATED` → `SUBMITTED` → `TRIAGED` → `MATCHING` → `ASSIGNED` → `ACCEPTED` → `IN_PROGRESS` → `FULFILLED` → `CONFIRMED` → `CLOSED`. Exceptions: `CANCELLED`, `DECLINED`, `EXPIRED`, `UNFULFILLABLE`, `ESCALATED`.
- **Required:** `service_request_id`, `support_case_id`, `category` (`FOOD`|`TRANSPORTATION`|`SHELTER`|`PEER_SUPPORT` in MVP), `status`, `created_at`.
- **Optional:** details text, destination (transport), quantity.
- **Sensitive:** details, destination.
- **Authz:** case authz; assigned provider (limited).
- **Spec:** [DISPATCH.md](DISPATCH.md).

## ServiceProvider

- **Purpose:** Party that can fulfill a Service Request.
- **Owner:** Administration / Resources.
- **Lifecycle:** `ACTIVE` → `SUSPENDED` → `ARCHIVED`.
- **Required:** `service_provider_id`, `organization_id` (or individual user link), `name`, `status`.
- **Authz:** org-admin, responders (read), SUAS-admin.

## ServiceOffer

- **Purpose:** A provider's offering of a category (capacity, hours).
- **Owner:** Resources.
- **Required:** `service_offer_id`, `service_provider_id`, `category`, `active`.
- **Optional:** capacity, hours.
- **Authz:** same as Resource.

## ServiceFulfillment

- **Purpose:** Record of acceptance, start, completion, confirmation, failure, partial, cancellation.
- **Owner:** Fulfillment.
- **Lifecycle:** `ACCEPTED` → `STARTED` → `COMPLETED` → `CONFIRMED` | `DISPUTED`, or `FAILED` | `PARTIAL` | `CANCELLED`.
- **Required:** `service_fulfillment_id`, `service_request_id`, `status`, `accepted_at`.
- **Optional:** `started_at`, `completed_at`, `veteran_confirmed_at`, `responder_confirmed_at`, `failure_reason`.
- **Authz:** assigned responder, assigned provider, veteran (confirm/dispute).
- **Spec:** [FULFILLMENT.md](FULFILLMENT.md).
- **Note:** funding fields are `FUTURE`.

## Resource

- **Purpose:** Catalog entry for an available support offering.
- **Owner:** Resources.
- **Lifecycle:** `ACTIVE` ↔ `INACTIVE`; never silently deleted if referenced.
- **Required:** `resource_id`, `organization_id`, `service_name`, `category`, `active`, `last_verified_at`, `verification_source`.
- **Optional:** `eligibility`, `counties`, `coverage_geometry`, `hours`, `contact_method`, `referral_method`, `cost`, `capacity`.
- **Authz:** responders (read); org-admin of owning org (write); SUAS-admin; veteran sees only non-sensitive public fields listed in [RESOURCES.md](RESOURCES.md).
- **Freshness:** <30 / 30–90 / >90 days as operational recommendations.

## ResourceCategory

- **Purpose:** Category enumeration and display metadata.
- **Owner:** Resources.
- **Required:** `category_code` (MVP: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`).
- **Future codes reserved:** `BENEFITS`, `HOUSING`, `HEALTHCARE_NAVIGATION`, `COMMUNITY`, `OTHER`.

## Referral

- **Purpose:** Directed handoff. Distinct from Service Request. Sending ≠ service received.
- **Owner:** Referrals.
- **Lifecycle:** `DRAFTED` → `SENT` → `ACKNOWLEDGED` → `ACCEPTED` | `DECLINED` → `COMPLETED` | `UNABLE_TO_SERVE` | `CANCELLED`.
- **Required:** `referral_id`, `support_case_id`, `destination_type`, `destination_id`, `reason`, `method`, `status`, `consent_grant_id`.
- **Optional:** `service_request_id`, `result_text`, `follow_up_id`.
- **Authz:** assigned responder; destination org (referral fields only); veteran (that a referral was sent, destination name).
- **Spec:** [REFERRALS.md](REFERRALS.md).

## Notification

- **Purpose:** One send attempt record.
- **Owner:** Notifications.
- **Lifecycle:** `QUEUED` → `SENT` | `FAILED` → `DELIVERED` | `BOUNCED` | `UNDELIVERABLE` (delivery_status as available).
- **Required:** `notification_id`, `recipient_user_id` (or address id), `reason`, `channel` (`EMAIL`|`SMS`|`IN_APP`), `consent_basis`, `template_version`, `created_at`.
- **Optional:** `sent_at`, `delivery_status`.
- **Sensitive:** address, body.
- **Authz:** system write; recipient read own; SUAS-admin audit.
- **Spec:** [NOTIFICATIONS.md](NOTIFICATIONS.md).

## NotificationPreference

- **Purpose:** Channel preferences. Preferences cannot grant consent; they only select channel when a grant exists.
- **Owner:** Notifications.
- **Required:** `notification_preference_id`, `user_id`, `channel`, `enabled`.
- **Authz:** self; system read.

## AuditEvent

- **Purpose:** Immutable who/what/when/which record.
- **Owner:** Audit / Event Layer.
- **Lifecycle:** append-only. No update, no ordinary delete.
- **Required:** envelope fields in [EVENT_MODEL.md](EVENT_MODEL.md).
- **Authz:** SUAS-admin; incident process. Not visible to trusted contacts.

## DomainEvent

- **Purpose:** Immutable business fact. Envelope in [EVENT_MODEL.md](EVENT_MODEL.md).
- **Owner:** Audit / Event Layer.
- **Lifecycle:** append-only.
- **Authz:** internal consumers; SUAS-admin.

## Pilot

- **Purpose:** Bounded operational trial configuration.
- **Owner:** Administration.
- **Required:** `pilot_id`, `name`, `geography` (Santa Clara County, California), `target_enrollment_min` (25), `target_enrollment_max` (50), `status`.
- **Authz:** SUAS-admin write; org-admin read if participating.

## PilotEnrollment

- **Purpose:** Veteran enrollment in a Pilot.
- **Owner:** Veteran Profiles / Administration.
- **Lifecycle:** `APPLIED` → `ENROLLED` → `WITHDRAWN` | `COMPLETED` | `REMOVED`.
- **Required:** `pilot_enrollment_id`, `pilot_id`, `veteran_profile_id`, `status`, `enrolled_at` (when enrolled).
- **Authz:** veteran (own), SUAS-admin, assigned responder (read).

## Feedback

- **Purpose:** Veteran or responder satisfaction / operational feedback. Not a clinical instrument.
- **Owner:** Administration.
- **Required:** `feedback_id`, `author_user_id`, `created_at`, `body` or structured scores listed in [ANALYTICS.md](ANALYTICS.md).
- **Optional:** `support_case_id`.
- **Sensitive:** free-text.
- **Authz:** author; SUAS-admin (aggregate and audit).

---

## Cross-cutting rules

1. Every tenant-owned entity has `tenant_id`.
2. Every mutable entity has `created_at`, `updated_at`.
3. Soft-delete uses `deleted_at` where specified; events never have `deleted_at`.
4. Actor on mutations is recorded via Domain Event / Audit Event, not by overwriting history.
5. Consent Grants are evaluated at use time, not cached as "once visible, always visible."
