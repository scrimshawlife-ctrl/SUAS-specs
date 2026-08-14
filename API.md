# API.md — Resource/domain contract (SUAS v0.1)

**Related:** [AUTH.md](AUTH.md), [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [EVENT_MODEL.md](EVENT_MODEL.md), [VERSIONING.md](VERSIONING.md), [SECURITY.md](SECURITY.md)

**Status:** `draft` / contract version `0.1.0`. This is a **resource/domain contract**, not an implementation. Transport is HTTPS JSON unless a later spec says otherwise.

---

## 1. Purpose

Define paths, authz, tenant scoping, pagination, errors, idempotency, validation, state-transition commands, audit, and versioning so `SUAS` can implement without inventing a second contract.

---

## 2. Path prefix

All paths below are relative to `/api/v0` while the contract is 0.x. A released 1.x may use `/api/v1`.

| Prefix | Domain |
|---|---|
| `/auth` | Challenges, verify, logout, MFA |
| `/veterans` | VeteranProfile, enrollments, own check-in entry points |
| `/check-ins` | Check-In resources |
| `/cases` | Support Cases, notes, assignments |
| `/service-requests` | Service Requests and fulfillment commands |
| `/resources` | Resource catalog |
| `/referrals` | Referrals |
| `/follow-ups` | Follow-Ups |
| `/trusted-contacts` | Trusted Circle |
| `/consents` | Consent Grants |
| `/notifications` | Notification reads / preferences |
| `/admin` | Administration |

---

## 3. Conventions

- Resource names match [GLOSSARY.md](GLOSSARY.md). Do not expose `/tickets` or `/alerts` as aliases.
- IDs are UUIDs in path params.
- JSON field names are snake_case matching [DATA_MODEL.md](DATA_MODEL.md).
- State-changing endpoints that are transitions are **commands**, not raw PATCH of `status`.

### 3.1 Command shape

`POST /cases/{id}/commands/claim`  
`POST /cases/{id}/commands/assign`  
`POST /cases/{id}/commands/escalate`  
`POST /cases/{id}/commands/resolve`  
`POST /service-requests/{id}/commands/submit`  
`POST /service-requests/{id}/commands/assign`  
`POST /service-requests/{id}/commands/accept`  
`POST /service-requests/{id}/commands/fulfill`  
`POST /service-requests/{id}/commands/confirm`  
`POST /consents/{id}/commands/revoke`

Illegal transitions return `409` with a machine-readable `error.code` = `ILLEGAL_TRANSITION`.

---

## 4. Authz and tenant scoping

- Every non-`/auth` request requires a session ([AUTH.md](AUTH.md)).
- Server derives `tenant_id` from the session/membership. Clients may not set an arbitrary tenant header to escape their scope.
- SUAS-admin may pass an audited `X-Tenant-Scope` (name illustrative) only when performing documented admin actions.
- Authorization: role + tenant + row + consent. Missing grant → `403` with `error.code` = `CONSENT_DENIED` (no payload leakage).

---

## 5. Pagination

List endpoints accept `cursor` + `limit` (default 20, max 100). Responses include `next_cursor`. Do not use unbounded offset on sensitive lists.

---

## 6. Errors

| HTTP | When |
|---|---|
| 400 | Validation |
| 401 | No/invalid session |
| 403 | Authenticated but not allowed (including consent deny) |
| 404 | Not found **or** hidden by tenant isolation (do not reveal existence across tenants) |
| 409 | Illegal transition / conflict |
| 429 | Rate limit |
| 500 | Unexpected |

Error body: `{ "error": { "code": "...", "message": "non-sensitive", "details": optional } }`.

---

## 7. Idempotency

Unsafe commands accept `Idempotency-Key`. Replays with the same key return the original result. Domain Events use the same key or a derived `event_id` ([EVENT_MODEL.md](EVENT_MODEL.md)).

---

## 8. Validation

- Unknown service category → 400
- Missing required settlement fields on resolve → 400
- Check-In answers for unknown `question_id` → 400
- Resource without `last_verified_at` → 400

---

## 9. Audit

Every successful command and every admin write emits Audit Events. Reads of sensitive fields (check-in answers, location, consent grants) emit Audit Events.

---

## 10. Versioning

Contract version in `VERSIONING.md`. Breaking path or semantics = MAJOR. Clients send `Accept: application/vnd.suas.v0+json` or rely on the path prefix — pick one in implementation and document it; do not silently support two conflicting contracts.

---

## 11. Representative resources (not exhaustive CRUD)

- `POST /auth/challenges` `POST /auth/verify` `POST /auth/logout` `POST /auth/mfa/verify`
- `GET /veterans/me` `PATCH /veterans/me`
- `POST /check-ins` `POST /check-ins/{id}/responses` `POST /check-ins/{id}/commands/complete`
- `GET /cases` `GET /cases/{id}` `POST /cases` + commands in §3.1
- `GET /service-requests` `POST /cases/{id}/service-requests` + commands
- `GET /resources` `POST /admin/resources` `POST /admin/resources/{id}/commands/verify`
- `POST /referrals` `POST /referrals/{id}/commands/send`
- `POST /follow-ups` `POST /follow-ups/{id}/commands/complete`
- `POST /trusted-contacts` `POST /trusted-contacts/{id}/commands/accept`
- `POST /consents` `POST /consents/{id}/commands/revoke`
- `GET /notifications` `PUT /notifications/preferences`
- `GET /admin/audit-events` `POST /admin/questionnaire-versions/{id}/commands/publish`

---

## 12. Non-goals

- Public unauthenticated resource dump
- GraphQL in MVP (`FUTURE` if ever)
- Vendor-specific webhooks as the contract

---

## 13. Testability

API suite in [TESTING.md](TESTING.md): authz matrix, tenant 404, illegal transition 409, idempotent retry, consent deny 403.
