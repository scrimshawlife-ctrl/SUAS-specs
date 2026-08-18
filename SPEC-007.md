# SPEC-007.md — Owner review worksheet: core architecture, API, auth, notifications, admin

**Status:** `BLOCKED_BY_SPEC_006`  
**Stack version:** `0.1.0` / `draft`  
**Owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-007  
**Required:** [ARCHITECTURE.md](ARCHITECTURE.md), [API.md](API.md), [APIS.md](APIS.md), [AUTH.md](AUTH.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [ADMIN.md](ADMIN.md)

Preflight only. Do not accept before SPEC-006 is accepted. Agents do not tick owner checkboxes or change lifecycle.

---

## 1. Objective

Accept the production-facing application/API/auth/notification/admin contract on top of the SPEC-006 logical model without choosing cloud, queue, auth, email, SMS, or service-provider vendors.

---

## 2. Owner checklist

### Architecture
- [ ] Scalable modular monolith remains the default; microservices require measured need.
- [ ] Application correctness state is stateless across app instances.
- [ ] Production-critical async work is durable.
- [ ] Settlement and Command Idempotency have explicit logical owners.
- [ ] Provider Router owns adapters/config, not Service Request state.

### API
- [ ] `/api/v0` path prefix is the canonical v0 version selector; no ambiguous dual versioning scheme.
- [ ] Growing lists are cursor-paginated/bounded.
- [ ] Unsafe commands use persistent `Idempotency-Key` semantics.
- [ ] Same key/same request replays authoritative result; conflicting reuse fails.
- [ ] `event_id` is distinct from command idempotency identity.
- [ ] Stale-state/contested transitions produce deterministic conflicts and no partial effects.
- [ ] Resolve creates one durable Settlement resolution cycle atomically and is idempotent on retry.
- [ ] Reopen preserves prior Settlement history.

### Auth
- [ ] Veteran passwordless boundary remains correct under D-003/D-004/D-016.
- [ ] Auth challenge is single-use and atomically consumed.
- [ ] Concurrent verify produces at most one success effect.
- [ ] Session/revocation truth is shared across horizontal app instances.
- [ ] User/membership revoke invalidates affected sessions without relying on process-local state.
- [ ] Privileged roles require MFA.
- [ ] Rate-limit correctness does not depend on one app process.

### Notifications
- [ ] One Notification row = one logical send intent.
- [ ] Duplicate generating event/job can dedupe to one logical Notification.
- [ ] Notification delivery retries are distinct from Follow-Up coordination attempts.
- [ ] Consent/basis is checked before each external send attempt.
- [ ] Notification send work is durable and duplicate-job safe.
- [ ] Provider-specific delivery statuses remain adapter-local.
- [ ] Authenticated/deduplicated webhooks cannot generate new unauthorized sends.

### Administration
- [ ] Org Admin remains distinct from SUAS Admin.
- [ ] Provider adapters are enabled only through accepted capability + closed provider decision/configuration authority.
- [ ] Provider secrets are never returned from ordinary admin API/UI.
- [ ] Disabling an adapter preserves historical FulfillmentAttempts.
- [ ] Manual Adapter remains a first-class configurable path.
- [ ] Admin operational surfaces expose normalized queue/provider health, not clinical outcome scoring.
- [ ] All privileged writes are MFA-protected and audited.

### Provider neutrality / operations
- [ ] APIS inventory and API contract agree on Plane A vs Plane B boundaries.
- [ ] Provider booking/webhook endpoints remain adapter-local.
- [ ] Missing provider capability is exposed as unavailable/degraded/manual, never faked.
- [ ] No provider/cloud/queue/auth/email/SMS vendor is encoded as canonical architecture.

---

## 3. Explicit contract decisions made in preflight

Owner review should explicitly confirm:

1. `/api/v0` path prefix is the sole canonical v0 API version selector.
2. API command idempotency persists across app restarts/instances.
3. Case resolution creates/attaches one Settlement cycle transactionally; lost HTTP response retry cannot create another cycle.
4. Challenge consumption is one-winner atomic.
5. Session revoke/rate-limit correctness is shared across horizontally scaled instances.
6. Notification logical-send identity/dedupe is distinct from transport attempts.
7. Provider adapter administration exposes normalized configuration/health but not secrets/vendor-domain state.

---

## 4. Decisions still open

This stage does not close:
- D-001 hosting;
- D-002 auth provider;
- D-003 SMS provider;
- D-004 email provider;
- D-005 DB hosting;
- D-017–D-020 service-provider adapters;
- D-022 durable job/queue implementation;
- D-023 SLOs;
- D-024 RTO/RPO.

---

## 5. Non-goals

No application code, ORM choice, physical migration, vendor selection, secret-management implementation, or lifecycle advancement.

---

## 6. Exit criteria

SPEC-007 may be owner-accepted only after SPEC-006 acceptance and after all checklist items are reviewed with no contradiction across architecture/API/APIS/auth/notifications/admin.

**Implementation handoff:** none until SPEC-016 release.
