# APIS.md — Necessary-API inventory (SUAS v0.1)

**Related:** [API.md](API.md), [ARCHITECTURE.md](ARCHITECTURE.md), [AUTH.md](AUTH.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md), [DATA_MODEL.md](DATA_MODEL.md), [DEPLOYMENT.md](DEPLOYMENT.md), [DECISIONS.md](DECISIONS.md), [ONBOARDING.md](ONBOARDING.md), [COMPLIANCE.md](COMPLIANCE.md), [SAFETY.md](SAFETY.md), [FRICTION.md](FRICTION.md)

**Status:** `draft` / `0.1.0`. This file inventories APIs/capabilities required to make the MVP loop work. It is not a second domain contract.

| File | Role |
|---|---|
| [API.md](API.md) | SUAS resource/domain contract: paths, authz, commands, errors |
| [APIS.md](APIS.md) | Necessary API/capability inventory |
| [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md) | Provider-neutral fulfillment port and adapter contract |

---

## 1. Two planes

```text
Plane A  SUAS product API          we implement; contract in API.md
Plane B  External capabilities     adapters; vendors DECISION_PENDING
```

Domain modules call SUAS-owned capability ports. Vendor SDKs and vendor payloads live only inside adapters.

---

## 2. Plane A — minimum SUAS product API

Paths below are relative to `/api/v0` while the contract is 0.x. [API.md](API.md) remains authoritative for exact semantics.

### 2.1 SIGNAL

| Method / path | Why required |
|---|---|
| `POST /auth/*` | Identify actor; representative challenge/verify/logout/MFA paths |
| `GET /veterans/me` | Veteran self-profile / enrollment state |
| `POST /veterans/me/commands/complete-enrollment` | First-run enrollment command |
| `POST /check-ins` | Start Check-In |
| `POST /check-ins/{id}/responses` | Record answers |
| `POST /check-ins/{id}/commands/complete` | Complete Check-In and trigger deterministic signal computation |

There is no generative `POST /signals/generate`. Primary Support Signal computation is internal, deterministic, versioned, and safety-bounded.

### 2.2 NEED

| Method / path | Why required |
|---|---|
| `POST /cases` | Open Support Case |
| `POST /cases/{id}/service-requests` | Record Service Request |
| `POST /service-requests/{id}/commands/submit` | Submit request |

An explicit veteran request may create NEED without a completed Check-In when permitted by the domain specs.

### 2.3 CONSENT

| Method / path | Why required |
|---|---|
| `POST /consents` | Create Consent Grant |
| `POST /consents/{id}/commands/revoke` | Revoke grant; stop future use |
| `POST /trusted-contacts` | Invite Trusted Contact |
| `POST /trusted-contacts/{id}/commands/accept` | Accept invite |

Membership alone grants no visibility.

### 2.4 COORDINATION

| Method / path | Why required |
|---|---|
| `GET /cases` | Responder queue; bounded/paginated in production |
| `GET /cases/{id}` | Case detail |
| `POST /cases/{id}/commands/claim` | Atomic `CLAIM_CASE` |
| `POST /cases/{id}/commands/assign` | Assign case |
| `POST /cases/{id}/commands/escalate` | Escalate |
| `GET /resources` | Resource catalog; bounded/paginated |
| `POST /referrals` | Draft Referral |
| `POST /referrals/{id}/commands/send` | Send Referral with active grant |
| `POST /cases/{id}/commands/log-contact-attempt` | Record responder contact attempt |
| `POST /cases/{id}/commands/complete-contact` | Record completed contact |

A Case Note is not a substitute for a contact log.

### 2.5 FULFILLMENT

| Method / path | Why required |
|---|---|
| `POST /service-requests/{id}/commands/assign` | Bind provider/offer; assignment is not fulfillment |
| `POST /service-requests/{id}/commands/accept` | Provider/responder acceptance |
| `POST /service-requests/{id}/commands/fulfill` | Record fulfillment outcome |
| `POST /service-requests/{id}/commands/confirm` | Veteran/responder confirmation |

External provider operations are implementation-internal adapter actions governed by [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md). Vendor-specific booking endpoints are not Plane A product RPCs.

### 2.6 FOLLOW-UP

| Method / path | Why required |
|---|---|
| `POST /follow-ups` | Create first-class Follow-Up |
| `POST /follow-ups/{id}/commands/complete` | Complete Follow-Up |

### 2.7 SETTLEMENT

| Method / path | Why required |
|---|---|
| `POST /cases/{id}/commands/resolve` | Resolve Case; Settlement fields required |

### 2.8 ADMIN

| Method / path | Why required |
|---|---|
| `GET /admin/audit-events` | Read Audit Events; bounded/paginated |
| `POST /admin/resources` | Create Resource |
| `POST /admin/resources/{id}/commands/verify` | Verify Resource freshness |
| `POST /admin/questionnaire-versions/{id}/commands/publish` | Publish questionnaire |
| `GET /admin/bootstrap/status` | Read first-run bootstrap state |
| `POST /admin/bootstrap/commands/complete-step` | Complete/audit bootstrap step |

### 2.9 NOTIFICATIONS

| Method / path | Why required |
|---|---|
| `GET /notifications` | Recipient reads own notifications; bounded/paginated |
| `PUT /notifications/preferences` | Channel preference; cannot authorize send |

Sends are event/job-driven; there is no public `POST /notifications/send`.

---

## 3. Plane B — external capability inventory

Vendors are `DECISION_PENDING`. Inventory is by capability, not brand.

### 3.1 Infrastructure/communications capabilities required for production MVP

| Capability ID | Need | Decision | Notes |
|---|---|---|---|
| `AUTH_CHALLENGE` | Magic link/email OTP/phone OTP/MFA | D-002 | No social-login assumption |
| `SMS_DELIVERY` | OTP + operational SMS | D-003 | Authenticated delivery webhooks; consent basis required |
| `EMAIL_DELIVERY` | Magic links + operational email | D-004 | Delivery/bounce webhooks |
| `RELATIONAL_DB` | PostgreSQL | D-005 | Logical engine fixed; hosting open |
| `APP_HOST` | Stateless modular-monolith application instances | D-001 | No vendor lock |
| `DURABLE_JOB_EXECUTION` | Durable async jobs/queue | D-022 | Production-critical work must survive worker restart; exact product open |

If SMS or email has no configured provider in an environment, mark the channel `UNAVAILABLE`; do not fake-send.

### 3.2 MVP service-fulfillment capabilities

These are **ports**, not commitments to a commercial provider, except where a released decision names a provider for adapter-local implementation. D-017 v0.1.2 selects Uber as the first API-backed transportation adapter family while preserving the required manual path.

| Capability ID | Canonical category | Port contract | Provider decision | Manual path |
|---|---|---|---|---|
| `TRANSPORTATION_FULFILLMENT` | `TRANSPORTATION` | `TransportationPort` | D-017: Uber selected for first API-backed adapter; manual remains required | Required |
| `TEMPORARY_SHELTER_FULFILLMENT` | `SHELTER` | `TemporaryShelterPort` | D-018 | Required |
| `FOOD_SUPPORT_FULFILLMENT` | `FOOD` | `FoodSupportPort` | D-019 | Required |
| `PEER_SUPPORT_FULFILLMENT` | `PEER_SUPPORT` | `PeerSupportPort` | D-020 if external; internal/manual QRF may satisfy | Required |

Rules:

1. The capability port exists even if the only configured adapter is `ManualAdapter`.
2. A service provider may support `API`, `WEBHOOK`, `DEEP_LINK`, `PHONE`, `EMAIL`, `MANUAL_COORDINATION`, or `NONE`.
3. Provider selection does not change Service Request states.
4. Provider-specific status is normalized inside the adapter.
5. External mutations use a Fulfillment Attempt idempotency key.
6. Consent/minimum-necessary projection is evaluated before disclosure.
7. Provider outage degrades to another compatible adapter or manual coordination when policy permits.

See [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md).

### 3.3 Conditional / future capabilities

| Capability ID | Status | Notes |
|---|---|---|
| `GEOCODE_MAPS` | D-014 `DECISION_PENDING` | County/coverage lists may suffice; do not assume maps API |
| `OBJECT_STORAGE` | Not required for current MVP | No attachments in MVP |
| `PUSH_PROVIDER` | `FUTURE` | PUSH channel future |
| `BILLING_ADAPTER` | `FUTURE` | Do not assert billability |
| `CACHE` | Optional / measured need | Correctness must not depend on cache unless later specified |
| `GENERATIVE_AI` | Forbidden for signal/safety/qualification/closure | Assistive use future only |

### 3.4 Explicitly not in MVP

Do not add clients for:

- 911 / PSAP / emergency dispatch APIs;
- VA benefits/health/identity APIs;
- Medi-Cal / clearinghouse / X12;
- EHR / clinical FHIR write;
- payment-card / checkout APIs;
- social login;
- contact-list/device-address-book APIs;
- continuous GPS/device telemetry.

---

## 4. Capability-port isolation rules

Examples:

- `SmsPort`
- `EmailPort`
- `AuthPort`
- `TransportationPort`
- `TemporaryShelterPort`
- `FoodSupportPort`
- `PeerSupportPort`

Rules:

1. Vendor SDKs live only in adapters.
2. Domain tests use fakes of the ports.
3. Vendor names do not appear in domain interfaces/types/state machines.
4. Infrastructure provider choices D-001–D-005 and D-022 are deployment decisions.
5. Fulfillment provider choices D-017–D-020 are adapter/configuration decisions.
6. Replacing one conforming adapter with another requires no domain rewrite.
7. `RELATIONAL_DB` is PostgreSQL as logical engine; hosting remains D-005.

---

## 5. Inbound webhooks

Webhook payloads are adapter-local, not product contracts.

All provider/notification webhooks must:

- authenticate signature/secret or equivalent;
- reject unverifiable requests;
- deduplicate retries;
- tolerate delayed/out-of-order delivery where applicable;
- map into SUAS-owned normalized status;
- never authorize new disclosure or bypass Consent;
- never directly bypass canonical Service Request/Fulfillment transitions;
- avoid logging full sensitive payloads.

Exact vendor webhook paths remain adapter configuration.

---

## 6. Production scalability rules for API boundaries

- Growing list endpoints are bounded and paginated.
- Externally consequential commands support idempotency where client/network retry can duplicate effects.
- External provider calls do not block user request paths when durable async execution is appropriate.
- Rate-limit responses from providers are surfaced to adapter/router backoff logic.
- Provider/notification bursts are absorbed by durable work queues with backpressure.
- No correctness-critical workflow state is held only in one application process.

See [SCALING.md](SCALING.md) and [RESILIENCE.md](RESILIENCE.md).

---

## 7. Onboarding commands

| Method / path | Actor | Effect |
|---|---|---|
| `GET /admin/bootstrap/status` | SUAS-admin | Read persisted checklist for environment |
| `POST /admin/bootstrap/commands/complete-step` | SUAS-admin | Record/audit step completion |
| `POST /veterans/me/commands/complete-enrollment` | Veteran | Complete authenticated pilot enrollment |

No public unauthenticated signup dump and no undocumented bootstrap flags.

---

## 8. Testability

| Check | Pass condition |
|---|---|
| Loop coverage | Each canonical stage has its minimum Plane A API |
| Port isolation | Domain packages import no provider SDKs |
| Adapter substitution | Two fakes for the same capability can be swapped with no domain change |
| Manual fallback | Each MVP fulfillment capability can operate through `ManualAdapter` |
| Provider idempotency | Duplicate job/client retry cannot create duplicate booking/dispatch for one Fulfillment Attempt |
| Unknown outcome | Timeout after possible provider acceptance reconciles before risky retry |
| Webhook auth/dedup | Unauthenticated callback rejected; duplicates safe |
| Consent projection | Adapter receives only consented/minimum fields |
| Provider outage | Service Request remains correct/visible and may reroute/manual-fallback |
| Durable jobs | Production-critical queued work survives worker restart |
| Pagination | Unbounded collections cannot be returned unbounded |
| Forbidden clients | No forbidden §3.4 clients present |
| No generative signal API | No public model-driven primary signal endpoint |

API contract tests remain in [TESTING.md](TESTING.md) and [API.md](API.md).

---

## 9. Non-goals

- Selecting specific ride, room, food, or peer-support vendors
- Requiring every service provider to expose an API
- Vendor-specific status as domain state
- Vendor-specific webhook payload as Plane A contract
- A public generative signal API
- Public `POST /notifications/send`
- GraphQL as an MVP requirement
