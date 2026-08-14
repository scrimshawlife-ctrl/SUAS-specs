# ARCHITECTURE.md — Modular monolith (pilot)

**Related:** [PRODUCT.md](PRODUCT.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [EVENT_MODEL.md](EVENT_MODEL.md), [API.md](API.md), [DEPLOYMENT.md](DEPLOYMENT.md), [SAFETY.md](SAFETY.md), [SETTLEMENT.md](SETTLEMENT.md)

**Status:** `draft` / `0.1.0`. Unsettled technology choices are `DECISION_PENDING`. Do not lock a cloud provider.

---

## 1. Purpose

Describe the runtime shape of SUAS for the Santa Clara County pilot (~25–50 veterans). The architecture is a **modular monolith**. No microservices without a demonstrated need recorded as a later spec change.

---

## 2. High-level shape

```
Veteran PWA
    │
    ▼
SUAS Application (modular monolith)
    Auth
    Veteran Profiles
    Check-ins
    Support Signals
    Consent
    Trusted Circle
    Cases
    Requests
    Dispatch
    Resources
    Referrals
    Fulfillment
    Follow-up
    Notifications
    Administration
    Audit / Event Layer
    │
    ▼
PostgreSQL
    │
    ▼
Background Jobs / Notifications
```

Responder and admin clients consume the same application modules through the [API.md](API.md) contract. They are not separate systems.

---

## 3. Style rules

1. One deployable application for the pilot.
2. One PostgreSQL database (logical). Module tables may be schema-namespaced but are not separately hosted.
3. Module boundaries are code and authorization boundaries, not network boundaries.
4. Extraction of a module to a service requires: demonstrated load or isolation need, a released spec change, and a migration plan. None of that exists today (`NOT_COMPUTABLE` as a need).
5. Cloud/platform = `DECISION_PENDING` (D-001, D-005). This file does not name a vendor.
6. Auth provider, SMS provider, email provider = `DECISION_PENDING` (D-002, D-003, D-004).

---

## 4. Clients

| Client | Users | Notes |
|---|---|---|
| Veteran PWA | Veteran | Check-In, consent management, request status, trusted-circle management, fulfillment confirmation. |
| Responder console | Responder, Organization Administrator | Coordination console, not an EHR. See [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md). |
| Admin console | SUAS System Administrator, Organization Administrator (scoped) | See [ADMIN.md](ADMIN.md). |

Native mobile apps are `FUTURE`. Push notifications are `FUTURE`.

---

## 5. Module catalog

Each module: purpose, owns, depends on, authz, sync vs async.

### 5.1 Auth

- **Purpose:** Identify users; issue and invalidate sessions; MFA for responder/admin; recovery.
- **Owns:** credentials-of-record (magic-link tokens, OTP challenges), sessions, MFA factors. Not veteran need data.
- **Depends on:** none (foundation).
- **Authz:** unauthenticated challenge endpoints; all other modules require a session.
- **Sync/async:** sync for challenge/verify; async for sending OTP/magic-link via Notifications.
- **Spec:** [AUTH.md](AUTH.md).

### 5.2 Veteran Profiles

- **Purpose:** VeteranProfile and PilotEnrollment.
- **Owns:** `VeteranProfile`, `PilotEnrollment`.
- **Depends on:** Auth, Administration (pilot config).
- **Authz:** veteran self; assigned responder (need-to-know fields); SUAS admin (audited).
- **Sync/async:** sync.

### 5.3 Check-ins

- **Purpose:** Versioned questionnaires and Check-In submissions.
- **Owns:** `QuestionnaireVersion`, `Question`, `AnswerOption`, `CheckIn`, `CheckInResponse`.
- **Depends on:** Veteran Profiles.
- **Authz:** veteran writes own; responders read only with Consent Grant or case-assignment policy as specified in [CONSENT.md](CONSENT.md).
- **Sync/async:** sync submit; async signal computation job.
- **Spec:** [CHECKINS.md](CHECKINS.md).

### 5.4 Support Signals

- **Purpose:** Deterministic signal computation and storage.
- **Owns:** `SupportSignal`.
- **Depends on:** Check-ins (inputs), Event Layer (emit `SUPPORT_SIGNAL_CHANGED`).
- **Authz:** same visibility as specified for signals in Consent.
- **Sync/async:** async job from check-in completion; sync read.
- **Spec:** [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md).

### 5.5 Consent

- **Purpose:** Consent Grants and Consent Events.
- **Owns:** `ConsentGrant`, `ConsentEvent`.
- **Depends on:** Veteran Profiles, Trusted Circle (subject of many grants).
- **Authz:** veteran manages own grants; all modules **must** query this module before share/notify.
- **Sync/async:** sync evaluate; async notify on grant/revoke.
- **Spec:** [CONSENT.md](CONSENT.md).

### 5.6 Trusted Circle

- **Purpose:** Invites, accepts, permissions, suspension, removal.
- **Owns:** `TrustedContact`.
- **Depends on:** Auth (contact user if enrolled), Consent, Notifications.
- **Authz:** veteran manages; contact sees own membership; responders do not enumerate the circle without a grant.
- **Sync/async:** sync mutations; async invite notifications.
- **Spec:** [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md).

### 5.7 Cases

- **Purpose:** Support Case lifecycle, notes, assignments at case level.
- **Owns:** `SupportCase`, `CaseAssignment`, `CaseNote`.
- **Depends on:** Veteran Profiles, Consent, Event Layer.
- **Authz:** assigned responder, org queue per [CASES.md](CASES.md), veteran (limited fields), SUAS admin (audited).
- **Sync/async:** sync commands; async notifications.
- **Spec:** [CASES.md](CASES.md).

### 5.8 Requests

- **Purpose:** Service Request records attached to a Support Case.
- **Owns:** `ServiceRequest`.
- **Depends on:** Cases, Consent.
- **Authz:** same case authz plus provider on assigned request (limited fields).
- **Sync/async:** sync commands.
- **Spec:** [DISPATCH.md](DISPATCH.md).

### 5.9 Dispatch

- **Purpose:** Documented Service Request transitions, matching, assignment, exceptions.
- **Owns:** transition logic (not a separate table beyond request status + events).
- **Depends on:** Requests, Resources, Fulfillment, Notifications, Event Layer.
- **Authz:** responder actions only; no hidden transitions.
- **Sync/async:** sync transition commands; async matching suggestions are `FUTURE` and must not be generative "smart matching". MVP matching is responder-selected from the catalog.
- **Spec:** [DISPATCH.md](DISPATCH.md).

### 5.10 Resources

- **Purpose:** Resource catalog and categories.
- **Owns:** `Resource`, `ResourceCategory`, `ServiceOffer`.
- **Depends on:** Organizations (via Administration).
- **Authz:** responders and org-admins (org-owned writes); veterans may see non-sensitive public fields of active resources when a referral is being discussed — exact veteran-visible field set is listed in [RESOURCES.md](RESOURCES.md).
- **Sync/async:** sync; freshness reports async.
- **Spec:** [RESOURCES.md](RESOURCES.md).

### 5.11 Referrals

- **Purpose:** Referral records distinct from Service Requests.
- **Owns:** `Referral`.
- **Depends on:** Cases, Requests (optional link), Resources, Consent, Notifications.
- **Authz:** assigned responder; destination org sees referral fields only.
- **Sync/async:** sync create/update; async notify destination if consented.
- **Spec:** [REFERRALS.md](REFERRALS.md).

### 5.12 Fulfillment

- **Purpose:** ServiceFulfillment records; confirmation; failure/partial/cancel.
- **Owns:** `ServiceFulfillment`.
- **Depends on:** Requests, Event Layer.
- **Authz:** assigned responder, assigned provider, veteran (confirm/dispute).
- **Sync/async:** sync commands.
- **Spec:** [FULFILLMENT.md](FULFILLMENT.md).

### 5.13 Follow-up

- **Purpose:** First-class Follow-Up work items.
- **Owns:** `FollowUp`.
- **Depends on:** Cases, Requests (optional), Notifications, Event Layer.
- **Authz:** responsible party, assigned responder, veteran when the follow-up is addressed to them.
- **Sync/async:** sync commands; async due/overdue jobs.
- **Spec:** [FOLLOWUP.md](FOLLOWUP.md).

### 5.14 Notifications

- **Purpose:** Record and send EMAIL, SMS, IN_APP. PUSH is `FUTURE`.
- **Owns:** `Notification`, `NotificationPreference`.
- **Depends on:** Consent (required), Auth (addresses).
- **Authz:** system writes; recipient reads own; no safety-critical logic in templates.
- **Sync/async:** enqueue async; API read sync.
- **Spec:** [NOTIFICATIONS.md](NOTIFICATIONS.md).

### 5.15 Administration

- **Purpose:** Orgs, membership, pilot, templates, system config.
- **Owns:** `Organization`, `OrganizationMembership`, `ResponderProfile`, `User` (profile fields), `Pilot`, `PilotEnrollment` (shared with Veteran Profiles), `Feedback`, questionnaire/signal/consent/notification template publication.
- **Depends on:** Auth, Audit.
- **Authz:** org-admin scoped; SUAS-admin global. Org-admin ≠ SUAS-admin.
- **Sync/async:** sync.
- **Spec:** [ADMIN.md](ADMIN.md).

### 5.16 Audit / Event Layer

- **Purpose:** Immutable `DomainEvent` and `AuditEvent` append-only stores.
- **Owns:** `DomainEvent`, `AuditEvent`.
- **Depends on:** none (all modules emit here).
- **Authz:** write via internal API only; read by SUAS-admin and by scoped incident process. Not mutable.
- **Sync/async:** sync append in the same transaction as the domain write where feasible; if dual-write, the job must be exactly-once with idempotent `event_id`.
- **Spec:** [EVENT_MODEL.md](EVENT_MODEL.md).

---

## 6. Database ownership

- One PostgreSQL instance per environment (see [DEPLOYMENT.md](DEPLOYMENT.md)).
- Each module owns its tables. Cross-module reads go through module APIs or documented foreign keys, not ad-hoc joins that bypass authz.
- Tenant isolation: `tenant_id` (pilot/org scope as defined in [DATA_MODEL.md](DATA_MODEL.md)) on every tenant-owned row. Row-level authz in [SECURITY.md](SECURITY.md).
- Soft-delete on mutable operational entities where specified. No soft-delete on events.

---

## 7. Background jobs

| Job | Trigger | Module | Failure handling |
|---|---|---|---|
| Compute Support Signal | Check-In completed | Support Signals | Retry; do not emit `SUPPORT_SIGNAL_CHANGED` until persisted |
| Due Follow-Up scan | Periodic | Follow-up | Emit `FOLLOWUP_DUE`; notify responsible party if consented |
| Overdue Follow-Up escalation | Periodic | Follow-up | Per [FOLLOWUP.md](FOLLOWUP.md) |
| Notification send | Enqueued Notification | Notifications | Retry with recorded `delivery_status` |
| Notification retry | Failed send | Notifications | Bounded retries; then ops alert |
| Resource freshness report | Periodic | Resources | Operational metric only |
| Session expiry sweep | Periodic | Auth | Invalidate |
| Magic-link / OTP expiry | Periodic | Auth | Invalidate challenges |

Job runners are in-process or a single worker pool attached to the monolith. A separate job platform is `DECISION_PENDING` and not required for the pilot.

---

## 8. Notifications

Events and policies generate notifications. Templates do not contain safety-critical branching. Red-state behavior lives in [SAFETY.md](SAFETY.md) and the Cases/Signals modules, which then request a notification with an explicit template id.

---

## 9. Sync vs async summary

- **Synchronous:** auth verify, consent evaluate, all state-transition commands, reads, admin writes.
- **Asynchronous:** signal compute, notification send, follow-up due scan, freshness report, invite email/SMS.

A user-facing command must not return success for a state transition that has not been committed. Notifications may lag.

---

## 10. Authorization architecture

- Authentication in Auth module.
- Authorization is **role + tenant + row + consent**. Passing authentication is not authorization.
- Consent evaluation is mandatory for veteran-data disclosure and for trusted-contact alerts.
- See [AUTH.md](AUTH.md), [CONSENT.md](CONSENT.md), [SECURITY.md](SECURITY.md).

---

## 11. Audit

Every privileged admin action, every consent change, every case/request transition, every notification send attempt, and every data export emits an Audit Event and, where listed, a Domain Event. See [EVENT_MODEL.md](EVENT_MODEL.md).

---

## 12. Future extraction

A module may be extracted only after: measured need, released spec, data-ownership plan, and an API that preserves the contracts in this stack. Not planned for the pilot.

---

## 13. AI policy (architecture)

**No generative AI for safety-critical decisions.**

Safety-critical (must not be determined by a generative model):

- primary Support Signal
- suicidality or any clinical determination
- emergency intervention
- whether to notify a Trusted Contact
- service qualification
- case closure

Future assistive uses (not MVP; require a later spec): resource deduplication, note summarization, categorization, admin search, aggregate analytics. Assistive output is advisory, must be inspectable, and must not write state without a human command.

See [SAFETY.md](SAFETY.md).

---

## 14. Funding / billing adapter (architecture)

```
Fulfillment → Funding Eligibility → Funding Source → Optional Billing Adapter
```

**Status:** `FUTURE`. No billing adapter module in the monolith for MVP. Do not assert Medi-Cal billability. See [SETTLEMENT.md](SETTLEMENT.md) and [PRODUCT.md](PRODUCT.md).

---

## 15. Unsettled technology

| Topic | Status |
|---|---|
| Cloud provider | `DECISION_PENDING` |
| Auth provider vs in-house | `DECISION_PENDING` |
| SMS provider | `DECISION_PENDING` |
| Email provider | `DECISION_PENDING` |
| Database hosting | `DECISION_PENDING` |
| PWA framework | `DECISION_PENDING` |
| Job runner product | `DECISION_PENDING` (in-process acceptable for pilot) |

Do not encode a vendor name as architecture.

---

## 16. Non-goals

- Microservices
- Event-sourcing as the system of record (events are an immutable log; mutable operational tables are the working store)
- Multi-region active-active (`NOT_COMPUTABLE` as a need)
- VA, county, or Medi-Cal integrations
