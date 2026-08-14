# APIS.md — Necessary-API inventory (SUAS v0.1)

**Related:** [API.md](API.md), [ARCHITECTURE.md](ARCHITECTURE.md), [AUTH.md](AUTH.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [DATA_MODEL.md](DATA_MODEL.md), [DEPLOYMENT.md](DEPLOYMENT.md), [DECISIONS.md](DECISIONS.md), [ONBOARDING.md](ONBOARDING.md), [COMPLIANCE.md](COMPLIANCE.md), [SAFETY.md](SAFETY.md), [FRICTION.md](FRICTION.md)

**Status:** `draft` / `0.1.0`. This file is an inventory of APIs **required to make the MVP loop work**. It is not a second contract.

| File | Role |
|---|---|
| [API.md](API.md) | Resource/domain **contract** (paths, authz, errors, commands) |
| [APIS.md](APIS.md) | **Necessary-API inventory** (what must exist for the loop; two planes) |

Do not implement against this file alone. Paths and semantics live in [API.md](API.md). This file says which of those paths are the **minimum** for SIGNAL → NEED → CONSENT → COORDINATION → FULFILLMENT → FOLLOW-UP → SETTLEMENT, and which **external capabilities** the monolith must adapt.

---

## 1. Two planes

```
Plane A  SUAS product API          we implement; contract in API.md
Plane B  External service APIs     adapters; vendors DECISION_PENDING
```

Domain modules call **capability ports**. Vendor SDKs live only in adapters. See §6.

---

## 2. Plane A — SUAS product API (we implement)

Point at [API.md](API.md) as the contract. Paths below are relative to `/api/v0` while the contract is 0.x.

This is the **minimum endpoint set** mapped to the canonical loop. It is **not** exhaustive CRUD. Additional reads/writes in [API.md](API.md) remain valid; they are not required to close the loop.

### 2.1 SIGNAL

| Method / path | Why it is required |
|---|---|
| `POST /auth/*` | Identify the actor. Representative: `POST /auth/challenges`, `POST /auth/verify`, `POST /auth/logout`, `POST /auth/mfa/verify` ([AUTH.md](AUTH.md), [API.md](API.md) §11). |
| `GET /veterans/me` | Veteran self-profile; enrollment state. |
| `POST /veterans/me/commands/complete-enrollment` | First-run enrollment command ([ONBOARDING.md](ONBOARDING.md)). Not a public unauthenticated signup dump. |
| `POST /check-ins` | Start a Check-In. |
| `POST /check-ins/{id}/responses` | Record answers. |
| `POST /check-ins/{id}/commands/complete` | Complete a Check-In; triggers internal signal computation. |

**Support-signal computation is an internal deterministic function, not a public generative API.** There is no `POST /signals/generate`. Clients read the stored `SupportSignal` as a field on the Check-In / Case as specified in [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md). No generative model may produce the primary signal ([SAFETY.md](SAFETY.md), [ARCHITECTURE.md](ARCHITECTURE.md)).

### 2.2 NEED

| Method / path | Why it is required |
|---|---|
| `POST /cases` | Open a Support Case. |
| `POST /cases/{id}/service-requests` | Record a Service Request. |
| `POST /service-requests/{id}/commands/submit` | Move `CREATED` → `SUBMITTED`. |

A Service Request may be created **without** a completed Check-In if the veteran explicitly requests help ([ONBOARDING.md](ONBOARDING.md), [CHECKINS.md](CHECKINS.md)). NEED can start from an explicit request.

### 2.3 CONSENT

| Method / path | Why it is required |
|---|---|
| `POST /consents` | Create a Consent Grant. |
| `POST /consents/{id}/commands/revoke` | Revoke; stops future use. |
| `POST /trusted-contacts` | Invite a Trusted Contact. |
| `POST /trusted-contacts/{id}/commands/accept` | Contact accepts the invite. |

Membership alone grants no visibility ([TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md)).

### 2.4 COORDINATION

| Method / path | Why it is required |
|---|---|
| `GET /cases` | Responder queue. |
| `GET /cases/{id}` | Case detail. |
| `POST /cases/{id}/commands/claim` | `CLAIM_CASE`. |
| `POST /cases/{id}/commands/assign` | `ASSIGN_CASE`. |
| `POST /cases/{id}/commands/escalate` | `ESCALATE`. |
| `GET /resources` | Catalog for matching / referral. |
| `POST /referrals` | Draft a Referral. |
| `POST /referrals/{id}/commands/send` | Send; requires an `ACTIVE` grant. |

| `POST /cases/{id}/commands/log-contact-attempt` | `CONTACT_ATTEMPT`. Required: `at`, `channel`, `outcome`, `actor_id`. Emits `RESPONDER_CONTACT_LOGGED`. |
| `POST /cases/{id}/commands/complete-contact` | `CONTACT_COMPLETE`. Required: `at`, `channel`, `outcome`, `actor_id`. Emits `RESPONDER_CONTACT_LOGGED`. `outcome` must not be `PENDING`. |

A Case Note is **not** a substitute for a contact log. See [API.md](API.md) section 11.1 and [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md).

### 2.5 FULFILLMENT

| Method / path | Why it is required |
|---|---|
| `POST /service-requests/{id}/commands/assign` | Bind a provider / offer. Assignment is not fulfillment. |
| `POST /service-requests/{id}/commands/accept` | Provider / responder accepts. |
| `POST /service-requests/{id}/commands/fulfill` | Record fulfillment outcome. |
| `POST /service-requests/{id}/commands/confirm` | Veteran or responder confirms. |

### 2.6 FOLLOW-UP

| Method / path | Why it is required |
|---|---|
| `POST /follow-ups` | Create a first-class Follow-Up. |
| `POST /follow-ups/{id}/commands/complete` | Complete it. |

### 2.7 SETTLEMENT

| Method / path | Why it is required |
|---|---|
| `POST /cases/{id}/commands/resolve` | Case → `RESOLVED`. Settlement fields are **required** ([SETTLEMENT.md](SETTLEMENT.md), [API.md](API.md) §8). |

### 2.8 ADMIN

| Method / path | Why it is required |
|---|---|
| `GET /admin/audit-events` | Read Audit Events. |
| `POST /admin/resources` | Create a Resource. |
| `POST /admin/resources/{id}/commands/verify` | Set `last_verified_at` + `verification_source`. |
| `POST /admin/questionnaire-versions/{id}/commands/publish` | Publish a QuestionnaireVersion (required before veteran enrollment). |
| `GET /admin/bootstrap/status` | First-run checklist status ([ONBOARDING.md](ONBOARDING.md)). |
| `POST /admin/bootstrap/commands/complete-step` | Persist and audit a bootstrap step. |

Bootstrap commands are **not** hidden flags. They emit Audit Events. See [ONBOARDING.md](ONBOARDING.md).

### 2.9 NOTIFICATIONS

| Method / path | Why it is required |
|---|---|
| `GET /notifications` | Recipient reads own notifications. |
| `PUT /notifications/preferences` | Channel preference (cannot authorize a send). |

Sends are **event-driven**, not a public `send` RPC. There is no `POST /notifications/send` for clients.

---

## 3. Plane B — External service APIs (adapters)

Vendors are `DECISION_PENDING`. Inventory is by **capability**, not vendor name. Do not lock a vendor in this file.

### 3.1 Required for MVP operations

| Capability ID | Need | Used for | Decision | Notes |
|---|---|---|---|---|
| `AUTH_CHALLENGE` | Magic link / email OTP / phone OTP / MFA | [AUTH.md](AUTH.md) | D-002 | No social login |
| `SMS_DELIVERY` | SMS OTP + operational SMS | [NOTIFICATIONS.md](NOTIFICATIONS.md), [AUTH.md](AUTH.md) | D-003 | Delivery webhooks; `consent_basis` required |
| `EMAIL_DELIVERY` | Magic links + operational email | [AUTH.md](AUTH.md), [NOTIFICATIONS.md](NOTIFICATIONS.md) | D-004 | Delivery / bounce webhooks |
| `RELATIONAL_DB` | PostgreSQL | [DATA_MODEL.md](DATA_MODEL.md) | D-005 | Not an HTTP API; required data plane |
| `APP_HOST` | Run the modular monolith + jobs | [DEPLOYMENT.md](DEPLOYMENT.md) | D-001 | No vendor lock |

If `SMS_DELIVERY` or `EMAIL_DELIVERY` has no provider in an environment, mark that channel `UNAVAILABLE`. Do not fake-send ([ONBOARDING.md](ONBOARDING.md), [COMPLIANCE.md](COMPLIANCE.md)).

### 3.2 Conditional / not required for the Santa Clara MVP

Unless a later spec says otherwise:

| Capability ID | Status | Notes |
|---|---|---|
| `GEOCODE_MAPS` | D-014 `DECISION_PENDING` | `coverage_geometry` is optional ([RESOURCES.md](RESOURCES.md)). A county list may suffice for the Santa Clara pilot. Do not guess that a maps API is required. |
| `OBJECT_STORAGE` | Not required | No attachments in MVP. |
| `PUSH_PROVIDER` | `FUTURE` | APNs / FCM reserved. `PUSH` channel is `FUTURE` ([NOTIFICATIONS.md](NOTIFICATIONS.md)). |
| `BILLING_ADAPTER` | `FUTURE` | [SETTLEMENT.md](SETTLEMENT.md). Do not assert billability. |
| `GENERATIVE_AI` | Forbidden for signal / safety / qualification / closure | Assistive uses are `FUTURE` and **not required** for MVP ([SAFETY.md](SAFETY.md), [ARCHITECTURE.md](ARCHITECTURE.md)). |

### 3.3 Explicitly not in MVP

Do **not** add clients for:

- 911 / PSAP / emergency dispatch APIs
- VA APIs (benefits, health, identity)
- Medi-Cal / clearinghouse / X12
- EHR / FHIR clinical write
- Payment card / Stripe-like checkout
- Social login (Google / Apple / Facebook)
- Contact-list / device-address-book APIs
- Continuous GPS / device-telemetry APIs

These are product non-goals ([PRODUCT.md](PRODUCT.md), [PRIVACY.md](PRIVACY.md), [SAFETY.md](SAFETY.md), [COMPLIANCE.md](COMPLIANCE.md)). Presence of a client in `SUAS` is a spec violation.

---

## 4. Adapter rule

Domain modules call **capability ports**. Examples (names illustrative of the port, not a vendor):

- `SmsPort`
- `EmailPort`
- `AuthPort`

Rules:

1. Vendor SDKs live **only** in adapters.
2. Domain tests use **fakes** of the ports.
3. Do **not** import a vendor name into domain specs or domain modules.
4. Selecting a vendor is D-001 through D-005. Closing a decision records the vendor in [DECISIONS.md](DECISIONS.md), not as an architecture rewrite.
5. `RELATIONAL_DB` is PostgreSQL as the logical engine ([ARCHITECTURE.md](ARCHITECTURE.md)). Hosting is D-005. The application talks SQL through the data layer, not a vendor HTTP API.

See [GLOSSARY.md](GLOSSARY.md) **Capability Port** and **External API**.

---

## 5. Inbound webhooks

SMS and email **delivery status** webhooks update `Notification.delivery_status` (`SENT` / `FAILED` / `DELIVERED` / `BOUNCED` / `UNDELIVERABLE`).

Rules:

1. Authenticate webhooks (shared secret, signature, or equivalent). Unauthenticated webhook posts are rejected.
2. A webhook **must not** be a back door around consent. Delivery status does not authorize a new send. Revoked grants still cancel in-flight sends at the send job ([NOTIFICATIONS.md](NOTIFICATIONS.md)).
3. Webhook payloads are not the product contract. Map vendor events inside the adapter; persist only the SUAS `delivery_status` and audit.
4. Do not log full message bodies ([SECURITY.md](SECURITY.md)).

Exact webhook path names are adapter-local (`DECISION_PENDING` until a vendor is chosen). They are not Plane A public RPC.

---

## 6. Onboarding commands (Plane A)

First-run is specified in [ONBOARDING.md](ONBOARDING.md). The commands are:

| Method / path | Actor | Effect |
|---|---|---|
| `GET /admin/bootstrap/status` | SUAS-admin | Read persisted checklist for **this environment**. |
| `POST /admin/bootstrap/commands/complete-step` | SUAS-admin | Record a step complete; emit Audit Event. |
| `POST /veterans/me/commands/complete-enrollment` | Veteran (authenticated) | Complete Pilot enrollment; not auto-enroll. |

Do not invent a public unauthenticated signup dump. Do not hide bootstrap behind undocumented flags.

---

## 7. Non-goals

- A second, conflicting contract
- Public unauthenticated resource dump
- GraphQL in MVP (`FUTURE` if ever)
- Vendor-specific webhooks as the product contract
- A public generative signal API
- A public `POST /notifications/send`
- Clients listed in §3.3

---

## 8. Testability

| Check | Pass condition |
|---|---|
| Loop coverage | Each canonical-loop stage has the minimum endpoints in §2 implemented or explicitly stubbed as `NOT_YET_RELEASED`. |
| No generative signal API | No public endpoint computes or "generates" a Support Signal via a model. |
| Port isolation | Domain tests depend on fakes of `SmsPort` / `EmailPort` / `AuthPort`; no vendor SDK import in domain packages. |
| Forbidden clients | Repository search finds no 911 / PSAP, VA, Medi-Cal / X12, FHIR-write, payment-card, social-login, address-book, or continuous-GPS clients. |
| Webhook auth | Unauthenticated delivery webhook → reject; status update does not enqueue a new send. |
| Consent on send | Adapter send path requires `consent_basis` already recorded on the Notification. |
| Bootstrap | `complete-step` without SUAS-admin + MFA → 403; success emits an Audit Event. |
| Enrollment | `complete-enrollment` without a session → 401; does not exist as an unauthenticated route. |
| Contact log | `log-contact-attempt` / `complete-contact` without active assignment → 403; missing `at`/`channel`/`outcome`/`actor_id` → 400; Case Note create does not emit `RESPONDER_CONTACT_LOGGED`. |

API contract tests remain in [TESTING.md](TESTING.md) and [API.md](API.md) §13.

---

## 9. Links

[API.md](API.md), [ARCHITECTURE.md](ARCHITECTURE.md), [AUTH.md](AUTH.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [DATA_MODEL.md](DATA_MODEL.md), [DEPLOYMENT.md](DEPLOYMENT.md), [DECISIONS.md](DECISIONS.md), [ONBOARDING.md](ONBOARDING.md), [COMPLIANCE.md](COMPLIANCE.md), [SAFETY.md](SAFETY.md), [RESOURCES.md](RESOURCES.md), [SETTLEMENT.md](SETTLEMENT.md), [GLOSSARY.md](GLOSSARY.md)
