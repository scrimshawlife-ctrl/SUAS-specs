# FOLLOWUP.md — First-class Follow-Up (SUAS v0.1)

**Status:** `draft` / `0.1.0` / SPEC-005 preflight; not implementation authority.  
**Related:** [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [SETTLEMENT.md](SETTLEMENT.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [EVENT_MODEL.md](EVENT_MODEL.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md)

**Actors:** Responder, Veteran when addressed, Org-admin, System due/overdue jobs.

---

## 1. Purpose

A Follow-Up is a first-class coordination work item. It is **not** a Case Note, Notification, Contact Attempt, or Settlement.

---

## 2. States

`SCHEDULED` → `DUE` → `COMPLETED` | `RESCHEDULED` | `OVERDUE` → `ESCALATED` | `CANCELLED`

| State | Meaning |
|---|---|
| `SCHEDULED` | Future `due_at` |
| `DUE` | Due time reached and work remains open |
| `COMPLETED` | Follow-Up action completed |
| `RESCHEDULED` | Due time changed with reason/history |
| `OVERDUE` | Past due and incomplete |
| `ESCALATED` | Follow-Up escalation performed |
| `CANCELLED` | Work intentionally ended; reason required |

---

## 3. Core fields and responsibility

Required:

- `support_case_id`
- `due_at`
- `responsible_type` — the closed set `RESPONDER|VETERAN|ORG_ADMIN|SYSTEM` (0.1.4)
- `responsible_id` — references the actor identified by `responsible_type`
- `status`

Optional:

- `service_request_id`
- `referral_id` FK (0.1.4) when the Follow-Up is the check-back for a Referral ([REFERRALS.md](REFERRALS.md) §6, [DATA_MODEL.md](DATA_MODEL.md) §6)
- completion/cancellation/reschedule reason metadata

Veteran may see prompts addressed to them under the MVP visibility rules.

---

## 4. Retry semantics

The coordination counter is named `coordination_attempt_count` (0.1.4, matching [DATA_MODEL.md](DATA_MODEL.md) §6; earlier drafts called it `retry_count`). It means **coordination-attempt retry count**, not infrastructure/message-delivery attempts.

Examples that may increment `coordination_attempt_count`:

- responder attempted the required check-back and did not reach the veteran/provider;
- a scheduled coordination action was attempted but could not be completed and will be retried.

The following do **not** increment `coordination_attempt_count`:

- SMS/email provider send retries;
- webhook retries;
- queue redelivery;
- worker restart/replay.

Notification delivery attempts belong to [NOTIFICATIONS.md](NOTIFICATIONS.md) / Audit Events. Job delivery attempts are operational telemetry. Do not mix those counters into Follow-Up business meaning.

Retry bound remains `DECISION_PENDING`; any temporary recommendation is not a released product rule.

---

## 5. Due/overdue durable jobs

Production due/overdue evaluation is durable asynchronous work.

Rules:

1. Duplicate job delivery is idempotent.
2. A stale due job must not move a `COMPLETED`, `CANCELLED`, or rescheduled Follow-Up to `DUE`/`OVERDUE`.
3. State/current `due_at` is re-checked atomically at mutation time.
4. Only the first logical transition emits the corresponding Domain Event.
5. Notification failure does not roll back the Follow-Up state transition.
6. Delayed/failed scan execution is observable through operations/queue-age telemetry.
7. A reschedule invalidates old due-work identities so an old job cannot mark the new schedule overdue.

---

## 6. Completion, reschedule, cancellation

### Completion

- actor + `completed_at` required;
- duplicate completion command is idempotent;
- emit one logical `FOLLOWUP_COMPLETED`.

### Reschedule

- reason + new `due_at` required;
- reschedule returns the Follow-Up to `SCHEDULED` with a bumped `schedule_version` (0.1.4); the `RESCHEDULED` value is retained for schema fidelity but is **not** a resting status, so a due-sweep selecting `SCHEDULED` still finds a rescheduled Follow-Up;
- prior due-time history remains inspectable through Audit/Domain history as accepted;
- new schedule gets a new durable due-work identity/version so old queued work becomes stale.

### Cancellation

- reason + actor required;
- cancellation is explicit, not inferred from Case close;
- duplicate cancellation is idempotent.

---

## 7. Escalation

Escalation may occur from `OVERDUE` or through an explicit authorized manual action defined by the owning workflow.

- Follow-Up escalation does not automatically rewrite parent Case state unless the documented Case escalation command also succeeds.
- If the parent Case is escalated, `CASE_ESCALATED` semantics apply separately.
- Org-admin/queue notification follows consent/system basis and notification policy.

---

## 8. Case interaction

When only Follow-Up remains, a Support Case may move to `FOLLOWUP`.

A Case cannot `RESOLVE` while a blocking Follow-Up remains `SCHEDULED`, `DUE`, `OVERDUE`, or otherwise open unless [SETTLEMENT.md](SETTLEMENT.md) explicitly permits it to remain as a documented post-settlement responsibility. The exact blocking flag/representation is handed to SPEC-006; implementation must not infer blocking from free text.

Case close never auto-completes a Follow-Up. Open work must be completed, cancelled, or explicitly carried forward under the Settlement rules.

---

## 9. Events

Existing canonical events:

- `FOLLOWUP_CREATED`
- `FOLLOWUP_DUE`
- `FOLLOWUP_COMPLETED`

`OVERDUE`, `RESCHEDULED`, `ESCALATED`, and `CANCELLED` are audited. Additional Domain Event names require explicit event-catalog reconciliation; implementation must not invent them silently.

---

## 10. Non-goals

- storing Follow-Up only as note text;
- using notification delivery retries as Follow-Up retry count;
- using queue redelivery as business retry meaning;
- auto-complete on Case close;
- clinical outcome measurement;
- deriving blocking/non-blocking state from note text.

---

## 11. Testability

- create without `due_at`/responsible party fails;
- duplicate due job emits one logical `FOLLOWUP_DUE`;
- stale old-schedule job cannot mark rescheduled Follow-Up due/overdue;
- completed/cancelled Follow-Up ignores stale due jobs;
- notification retry does not increment Follow-Up coordination retry count;
- duplicate completion/cancellation is idempotent;
- Case cannot resolve with blocking open Follow-Ups unless Settlement contract explicitly carries them forward;
- Case Note cannot substitute for Follow-Up in reporting.
