# EVENT_MODEL.md — Domain Events and Audit Events (SUAS v0.1)

**Status:** `draft` / `0.1.0` / SPEC-003 preflight; not implementation authority.  
**Related:** [DATA_MODEL.md](DATA_MODEL.md), [ARCHITECTURE.md](ARCHITECTURE.md), [VERSIONING.md](VERSIONING.md), [SECURITY.md](SECURITY.md), [API.md](API.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md)

---

## 1. Purpose

Record immutable business facts (Domain Events) and immutable who/what/when security/operations facts (Audit Events).

Event stores are append-only from application roles. Application code cannot UPDATE/DELETE an existing event's business meaning.

---

## 2. Envelope

Both event stores use a common envelope.

| Field | Required | Notes |
|---|---|---|
| `event_id` | yes | Unique immutable identifier for this persisted fact; **not** overloaded as the command/job idempotency key |
| `event_type` | yes | Domain catalog type or audit action type as applicable |
| `aggregate_type` | yes | e.g. `SupportCase`, `ServiceRequest`, `SupportSignal` |
| `aggregate_id` | yes | Identifier of the primary aggregate/fact target |
| `tenant_id` | yes | Tenant boundary |
| `actor_type` | yes | `VETERAN` / `RESPONDER` / `ORG_ADMIN` / `SUAS_ADMIN` / `TRUSTED_CONTACT` / `SERVICE_PROVIDER` / `SYSTEM` |
| `actor_id` | yes | User id or stable system actor identifier |
| `occurred_at` | yes | Server-authoritative event time |
| `schema_version` | yes | Event schema version; v0.1 starts at `0.1.0` |
| `payload` | yes | Structured and bounded; no secrets; minimize sensitive free-text |
| `idempotency_key` | conditional | Stable command/job/external-attempt identity when an event is produced by retryable work |
| `correlation_id` | optional | Groups facts belonging to one user/workflow/request across async boundaries |
| `causation_event_id` | optional | Prior Domain Event that directly caused this event |
| `request_id` | optional | Request/trace identifier when available; must not carry sensitive content |

### 2.1 Identity separation

`event_id`, `idempotency_key`, `correlation_id`, and `causation_event_id` have different meanings and must not be treated as aliases.

- `event_id` identifies one immutable persisted fact.
- `idempotency_key` identifies a retryable logical command/job/attempt whose duplicate execution must not create duplicate business meaning.
- `correlation_id` groups related operations.
- `causation_event_id` expresses direct event causality.

A producer replay may reuse the same logical idempotency key while returning/referencing the already-persisted event rather than inventing a second fact.

---

## 3. Domain event catalog

The following event types are currently defined in v0.1. Adding a type before first release is an additive contract change that must be reconciled across the owning domain spec, this catalog, data model, tests, and changelog.

| event_type | Typical aggregate |
|---|---|
| `VETERAN_ENROLLED` | VeteranProfile / PilotEnrollment |
| `CHECKIN_COMPLETED` | CheckIn |
| `SUPPORT_SIGNAL_CHANGED` | SupportSignal |
| `CASE_CREATED` | SupportCase |
| `CASE_ASSIGNED` | SupportCase / CaseAssignment |
| `RESPONDER_CONTACT_LOGGED` | SupportCase / ContactAttempt |
| `CASE_ESCALATED` | SupportCase |
| `CASE_RESOLVED` | SupportCase |
| `SERVICE_REQUEST_CREATED` | ServiceRequest |
| `SERVICE_REQUEST_ASSIGNED` | ServiceRequest |
| `SERVICE_ACCEPTED` | ServiceRequest / ServiceFulfillment |
| `SERVICE_FULFILLED` | ServiceRequest / ServiceFulfillment |
| `SERVICE_FAILED` | ServiceRequest / ServiceFulfillment |
| `REFERRAL_CREATED` | Referral |
| `REFERRAL_UPDATED` | Referral |
| `FOLLOWUP_CREATED` | FollowUp |
| `FOLLOWUP_DUE` | FollowUp |
| `FOLLOWUP_COMPLETED` | FollowUp |
| `TRUSTED_CONTACT_INVITED` | TrustedContact |
| `CONSENT_GRANTED` | ConsentGrant |
| `CONSENT_REVOKED` | ConsentGrant |
| `TRUSTED_CONTACT_ALERTED` | TrustedContact |

This catalog is not permanently closed at 22 events while the stack is still draft. Later roadmap stages may add events for already-specified concepts (for example provider integration/recovery) only through an explicit additive spec change. Provider/vendor-native event names must never leak directly into this catalog.

---

### 3.1 `CHECKIN_COMPLETED`

Emitted only on the first successful logical transition of a Check-In to `COMPLETED`.

Required/expected payload references:

- `check_in_id`
- `veteran_profile_id`
- `questionnaire_version`

Rules:

1. Repeating the same completion command does not emit another logical completion fact.
2. Async consumers use `event_id`/`idempotency_key` semantics to prevent duplicate downstream settlement.
3. The event requests durable Support Signal computation; emission does not mean the signal has already settled.

### 3.2 `SUPPORT_SIGNAL_CHANGED`

Represents a settled Support Signal domain change, not a worker-attempt record.

Expected payload references:

- `support_signal_id`
- `veteran_profile_id`
- `level`
- `signal_version`
- `input_questionnaire_version`
- source/computation reference sufficient to identify the logical calculation

Duplicate replay of an already-settled computation must not emit a second logical `SUPPORT_SIGNAL_CHANGED` fact.

### 3.3 `RESPONDER_CONTACT_LOGGED`

Emitted by `POST /cases/{id}/commands/log-contact-attempt` and `POST /cases/{id}/commands/complete-contact`.

Required payload fields:

| Field | Notes |
|---|---|
| `contact_attempt_id` | Row id |
| `at` | Contact timestamp |
| `channel` | `EMAIL` / `SMS` / `IN_APP` / `PHONE` |
| `outcome` | `PENDING` / `REACHED` / `NO_ANSWER` / `LEFT_MESSAGE` / `DECLINED` / `UNABLE` |
| `actor_id` | Responder user id |
| `command` | `log-contact-attempt` or `complete-contact` |

A Case Note create must not emit this event.

---

## 4. Audit Events

Audit Events use the common envelope plus `action`, `target_type`, `target_id`, and optional request metadata (`ip`, `user_agent`) where collection is justified.

They cover at least:

- auth success/failure, session invalidation, recovery;
- admin writes/publication;
- consent evaluate allow/deny for third-party disclosure;
- provider disclosure allow/deny and provider mutation attempts when later accepted specs require them;
- exports;
- notification send attempts;
- domain state transitions;
- recovery/replay/dead-letter actions that can change or restore observable system behavior.

Audit payloads remain minimum-necessary; do not copy whole provider responses, Check-In answers, notes, or secrets into event payloads.

---

## 5. Transactionality, replay, and idempotency

1. Event persistence is append-only with unique `event_id`.
2. Retryable producers use stable `idempotency_key` values and must resolve duplicates without duplicate logical business facts.
3. Domain state and its required Domain Event must be committed atomically **or** through an outbox/equivalent replay-safe pattern that cannot permanently lose the event after domain commit.
4. Async delivery is assumed at-least-once unless an implementation proves stronger guarantees. Consumers must therefore be idempotent.
5. Consumer retry count is operational metadata; it is not domain meaning.
6. Replaying an event must not re-run a non-idempotent external mutation without the owning domain/provider idempotency contract.
7. Retention is D-007 `DECISION_PENDING`; do not invent destructive event purge behavior.

The target is exactly-once **observable business effect**, not exactly-once infrastructure delivery.

---

## 6. Ordering

Global total ordering is not required.

Where ordering matters, consumers must use domain-specific version/state checks plus causal identifiers rather than assuming arrival order equals business order.

Provider/webhook delivery may be out of order under later specs; provider-native timestamps/status sequences remain adapter-local until normalized.

---

## 7. Notifications

Events and policies may enqueue notifications. Emitting an event is not itself a send and does not bypass Consent evaluation at send time.

---

## 8. Schema evolution

- `schema_version` is pinned on each event.
- Additive payload fields/events require documented compatibility and tests.
- Breaking envelope/meaning changes require the version impact defined in [VERSIONING.md](VERSIONING.md).
- Consumers must reject or safely ignore unsupported incompatible versions rather than silently misinterpret them.

---

## 9. Non-goals

- event sourcing as the sole system of record;
- PII-rich event payloads;
- global message ordering;
- exactly-once broker claims as a correctness requirement;
- vendor-native webhook events as canonical SUAS Domain Events.

---

## 10. Testability

- application roles cannot UPDATE/DELETE Audit/Domain Events;
- duplicate command/job replay does not duplicate logical Domain Events;
- domain commit cannot permanently lose its required event under crash/restart test;
- duplicate event delivery does not duplicate downstream state transition;
- unsupported schema version fails safely;
- causal/correlation fields remain distinct from event identity;
- event payload fixtures contain no secrets or unnecessary sensitive free-text.
