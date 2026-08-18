# API.md — Resource/domain contract (SUAS v0.1)

**Related:** [AUTH.md](AUTH.md), [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [SETTLEMENT.md](SETTLEMENT.md), [EVENT_MODEL.md](EVENT_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [VERSIONING.md](VERSIONING.md), [SECURITY.md](SECURITY.md), [APIS.md](APIS.md), [ONBOARDING.md](ONBOARDING.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md)

**Status:** `draft` / contract `0.1.0`. HTTPS JSON resource/domain contract. SPEC-007 is dependency-blocked; this is preflight reconciliation, not acceptance.

---

## 1. Contract principles

1. Canonical names match [GLOSSARY.md](GLOSSARY.md); no `/tickets` or signal `/alerts` aliases.
2. IDs in paths are UUIDs unless a version identifier is explicitly textual.
3. JSON fields are `snake_case`.
4. State transitions use command endpoints, not raw status PATCH.
5. Server derives tenant/actor authority; clients cannot choose arbitrary tenant scope.
6. Unsafe logical commands are persistently idempotent across process restart and horizontal instances.
7. Growing lists are cursor-paginated/bounded.
8. Provider SDK/webhook endpoints are adapter-local, not Plane A product contracts.

---

## 2. Version prefix

All v0 paths are relative to `/api/v0`.

The **path prefix is the canonical API version selector for v0**. Clients may send ordinary content negotiation headers, but no second media-type version contract is required. Implementation must not create two conflicting version selectors.

Breaking path/semantic change follows [VERSIONING.md](VERSIONING.md).

---

## 3. Resource prefixes

`/auth`, `/veterans`, `/check-ins`, `/cases`, `/service-requests`, `/resources`, `/referrals`, `/follow-ups`, `/trusted-contacts`, `/consents`, `/notifications`, `/admin`.

---

## 4. Authorization / tenancy

- Every non-auth request requires an authenticated session unless explicitly documented otherwise.
- Authorization = role + tenant + row + consent/system basis.
- Cross-tenant hidden resources return `404` or scoped denial without existence leakage.
- SUAS-admin cross-tenant action uses an explicitly audited scoped admin path; illustrative headers are not authority by themselves.
- Missing consent/basis for disclosure returns `403` with non-sensitive `CONSENT_DENIED`.

---

## 5. Pagination

List endpoints use `cursor` + `limit` (default 20, maximum 100 unless a later accepted endpoint-specific limit is lower). Responses return `next_cursor` when more data exists.

Cursor semantics must remain stable enough that pagination does not silently duplicate/omit records under ordinary concurrent inserts; exact implementation may use keyset pagination.

No unbounded sensitive list endpoint.

---

## 6. Errors

| HTTP | Meaning |
|---|---|
| 400 | validation / malformed request |
| 401 | missing/invalid/expired session |
| 403 | authenticated but unauthorized/consent denied |
| 404 | absent or tenant-hidden resource |
| 409 | illegal transition, stale-state conflict, idempotency payload conflict, contested winner lost |
| 422 | semantically valid shape but domain prerequisite missing where 409 is not appropriate |
| 429 | rate/backpressure limit |
| 500 | unexpected internal failure |
| 503 | explicitly degraded/unavailable dependency when no safe queued/manual path is available |

Body: `{ "error": { "code": "...", "message": "non-sensitive", "details": optional } }`.

Canonical conflict codes include `ILLEGAL_TRANSITION`, `STALE_STATE`, `ALREADY_CLAIMED`, `IDEMPOTENCY_CONFLICT`.

---

## 7. Persistent idempotency

Unsafe commands that can create/transition records or external consequences require `Idempotency-Key` unless the endpoint contract explicitly proves natural idempotency.

Rules:

1. Persistence uses `CommandIdempotencyRecord` semantics from [DATA_MODEL.md](DATA_MODEL.md), not process memory.
2. Scope includes tenant + logical command/route + actor/aggregate context as appropriate.
3. Same key + same canonical request fingerprint returns/reuses the original authoritative logical result.
4. Same key + conflicting request body/scope returns `409 IDEMPOTENCY_CONFLICT`.
5. A request that lost its network response may safely retry with the same key.
6. Domain `event_id` is separate from `Idempotency-Key`; event publication follows [EVENT_MODEL.md](EVENT_MODEL.md).
7. Provider external mutation idempotency is additionally bound to `FulfillmentAttempt` and must not be replaced by API-command idempotency.

Retention/expiry of idempotency records must not permit unsafe duplicate effects inside the accepted replay window; exact retention is tied to D-007/security policy.

---

## 8. Representative command surface

### Cases
- `POST /cases`
- `POST /cases/{id}/commands/claim`
- `POST /cases/{id}/commands/assign`
- `POST /cases/{id}/commands/log-contact-attempt`
- `POST /cases/{id}/commands/complete-contact`
- `POST /cases/{id}/commands/escalate`
- `POST /cases/{id}/commands/resolve`
- `POST /cases/{id}/commands/close`
- `POST /cases/{id}/commands/reopen`

### Service Requests
- `POST /cases/{id}/service-requests`
- `POST /service-requests/{id}/commands/submit`
- `POST /service-requests/{id}/commands/assign`
- `POST /service-requests/{id}/commands/accept`
- `POST /service-requests/{id}/commands/start`
- `POST /service-requests/{id}/commands/fulfill`
- `POST /service-requests/{id}/commands/confirm`
- documented cancel/decline/escalate/exception commands as required by [DISPATCH.md](DISPATCH.md)

### Other
- `POST /consents/{id}/commands/revoke`
- `POST /referrals/{id}/commands/send`
- `POST /follow-ups/{id}/commands/complete`
- `POST /trusted-contacts/{id}/commands/accept`
- `POST /veterans/me/commands/complete-enrollment`

Illegal/stale transitions return `409` and make no partial business-state change.

---

## 9. Settlement / resolution API

`POST /cases/{id}/commands/resolve` is the canonical MVP resolution command.

It accepts the required Settlement content from [SETTLEMENT.md](SETTLEMENT.md). Within one logical transaction it:

1. validates Case/request/follow-up prerequisites;
2. allocates the next deterministic `resolution_cycle` for the Case;
3. creates one durable Settlement row;
4. updates the Case to `RESOLVED` and current Settlement projection;
5. emits/audits the required business facts through replay-safe event semantics.

The same idempotency key must return the same Settlement/cycle on retry; it must never create a second resolution cycle for a lost HTTP response.

Reopen never edits the prior Settlement. A later resolve creates a later cycle.

Reads:
- `GET /cases/{id}/settlements` — bounded history according to actor visibility;
- `GET /cases/{id}/settlements/{settlement_id}` — one settlement;
- normal Case detail may expose `current_settlement_id`/current veteran-visible summary.

No raw PATCH of settled historical content after it has been used to enter `RESOLVED`.

---

## 10. Contact log commands

| Path | Required fields | Event |
|---|---|---|
| `POST /cases/{id}/commands/log-contact-attempt` | `at`, `channel`, `outcome`, actor bound from session | `RESPONDER_CONTACT_LOGGED` |
| `POST /cases/{id}/commands/complete-contact` | `at`, `channel`, non-`PENDING` outcome, actor bound from session | `RESPONDER_CONTACT_LOGGED` |

Active assignment required. A Case Note is not a Contact Attempt. Client-supplied `actor_id`, if accepted for compatibility, must match the authenticated actor or be rejected.

---

## 11. Representative reads/writes

- auth challenge/verify/logout/MFA paths;
- `GET/PATCH /veterans/me`;
- Check-In start/response/complete;
- bounded `GET /cases`, `GET /cases/{id}`, contact attempts, Settlement history;
- bounded `GET /service-requests`, `GET /resources`, `GET /notifications`;
- Resource admin create/verify;
- Referral create/send;
- Follow-Up create/complete;
- Trusted Contact create/accept;
- Consent create/revoke;
- Notification preferences;
- Admin audit/bootstrap/questionnaire/provider-adapter surfaces specified in [ADMIN.md](ADMIN.md).

---

## 12. Validation

At minimum:
- unknown category → 400;
- unknown questionnaire question/version misuse → 400;
- Resource without required freshness fields → 400;
- Resolve without Settlement required content/prerequisites → 400/409 as appropriate;
- stale expected state/version → 409;
- idempotency conflict → 409;
- cross-tenant identifiers never bypass scope.

---

## 13. Audit / correlation

Successful privileged commands and sensitive reads emit Audit Events as specified. Requests carry/generate `request_id`/correlation identity propagated into events/jobs without exposing sensitive payloads.

---

## 14. Backpressure

API may return `429` for rate/tenant fairness controls. Commands accepted for durable asynchronous external processing return a domain-appropriate pending result only when the canonical transition has committed and the contract permits pending external work.

Do not hold HTTP requests open indefinitely waiting for provider/notification completion.

---

## 15. Non-goals

- public unauthenticated resource dump;
- GraphQL in MVP;
- vendor-specific product webhook contract;
- provider status as hidden Service Request transition;
- in-process-only idempotency cache;
- ambiguous dual versioning schemes.

---

## 16. Testability

Required API contract tests include:
- authz/tenant isolation;
- bounded pagination;
- illegal/stale transition conflicts;
- two concurrent Case claims produce one winner;
- same idempotency key/same request replays original result across process/worker restart simulation;
- same idempotency key/different payload → `409 IDEMPOTENCY_CONFLICT`;
- resolve retry creates one Settlement resolution cycle;
- reopen preserves prior Settlement and later resolve creates a later cycle;
- consent denial has no payload leakage;
- Contact Attempt validation/authorization;
- provider callbacks cannot directly invoke undocumented Plane A transitions.
