# DISPATCH.md — Service Request state machine (SUAS v0.1)

**Status:** `draft` / `0.1.0` / SPEC-004 preflight; not implementation authority.  
**Related:** [CASES.md](CASES.md), [FULFILLMENT.md](FULFILLMENT.md), [RESOURCES.md](RESOURCES.md), [REFERRALS.md](REFERRALS.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [EVENT_MODEL.md](EVENT_MODEL.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md)

**Actors:** Veteran, Responder, authorized Service Provider identity/manual responder-on-behalf, System jobs.

---

## 1. Purpose

A **Service Request** is one specific requested need. It is not a Support Case, Referral, Fulfillment, Provider Offer, or Fulfillment Attempt.

MVP matching is responder-selected from the Resource/Provider catalog. No generative or unspecified "smart matching" is permitted.

---

## 2. States

Happy path:

`CREATED` → `SUBMITTED` → `TRIAGED` → `MATCHING` → `ASSIGNED` → `ACCEPTED` → `IN_PROGRESS` → `FULFILLED` → `CONFIRMED` → `CLOSED`

Exceptions:

`CANCELLED` | `DECLINED` | `EXPIRED` | `UNFULFILLABLE` | `ESCALATED`

Provider integration statuses (`PROVIDER_*`, `MANUAL_*`) belong to Fulfillment Attempt/integration state and are not Service Request states.

---

## 3. Command concurrency invariant

Every state-changing Service Request command is stale-state protected and idempotent.

Required semantics:

1. The mutation validates the expected current request state inside the same atomic write that performs the transition.
2. Concurrent incompatible commands yield one valid winner; losers receive conflict and create no partial event/effect.
3. Replaying the same logical command/idempotency key returns the original logical result.
4. Exactly one logical Domain Event is emitted for a transition where the catalog defines one.
5. Duplicate jobs/webhooks do not advance the state machine twice.
6. Provider callbacks are evidence inputs, not privileged raw state transitions.

---

## 4. Transitions

| Source | Target | Actor | Prerequisites | Domain Event |
|---|---|---|---|---|
| (none) | `CREATED` | Veteran / Responder | parent Case exists and is not `CLOSED`; creation intent idempotency passes | `SERVICE_REQUEST_CREATED` |
| `CREATED` | `SUBMITTED` | creator / assigned Responder | valid MVP category + required details | — |
| `SUBMITTED` | `TRIAGED` | assigned Responder | active Case assignment | — |
| `TRIAGED` | `MATCHING` | assigned Responder | — | — |
| `MATCHING` | `ASSIGNED` | assigned Responder | Resource/Service Provider/manual path selected; disclosure consent/basis checked if data leaves SUAS | `SERVICE_REQUEST_ASSIGNED` |
| `ASSIGNED` | `ACCEPTED` | authorized provider identity or assigned Responder-on-behalf | provider/manual acceptance evidence; actor/basis recorded | `SERVICE_ACCEPTED` |
| `ACCEPTED` | `IN_PROGRESS` | authorized provider identity or assigned Responder | Fulfillment `STARTED` evidence recorded | — |
| `IN_PROGRESS` | `FULFILLED` | authorized provider identity or assigned Responder | Fulfillment `COMPLETED` evidence recorded; assignment alone insufficient | `SERVICE_FULFILLED` |
| `FULFILLED` | `CONFIRMED` | Veteran and/or assigned Responder | confirmation satisfies [FULFILLMENT.md](FULFILLMENT.md) | — |
| `CONFIRMED` | `CLOSED` | assigned Responder | no unresolved confirmation dispute | — |
| `ASSIGNED` | `DECLINED` | authorized provider identity / assigned Responder-on-behalf | decline evidence + reason | — |
| `DECLINED` | `MATCHING` | assigned Responder | prior attempt terminal/recorded; deliberate rematch | — |
| `{CREATED, SUBMITTED, TRIAGED, MATCHING, ASSIGNED, ACCEPTED, IN_PROGRESS}` | `CANCELLED` | Veteran / assigned Responder | reason; cancellation authority checked | — |
| `{CREATED, SUBMITTED, TRIAGED, MATCHING, ASSIGNED}` | `EXPIRED` | System | documented TTL elapsed; stale-state check passes | — |
| `TRIAGED`/`MATCHING`/`ASSIGNED`/`DECLINED` | `UNFULFILLABLE` | assigned Responder | reason; no acceptable current path | — |
| `{TRIAGED, MATCHING, ASSIGNED, ACCEPTED, IN_PROGRESS}` | `ESCALATED` | assigned Responder | reason | may emit `CASE_ESCALATED` only when Case escalation is also performed |
| `ESCALATED` | `TRIAGED` or `MATCHING` | assigned Responder | escalation review complete; caller names the intended target | — |

Closed edge sets (0.1.4): cancellation, expiry, and escalation are the enumerated source sets in the table above (no wildcard). `RETURN_FROM_ESCALATION` has exactly two documented targets — `TRIAGED` and `MATCHING` — and the caller names the intended one. Implementation encodes these explicit sets, never a wildcard that could permit `CLOSED` or an invalid historical transition.

---

## 5. Provider integration relationship

Selecting a provider and obtaining provider-side fulfillment are separate concepts.

```text
Service Request MATCHING
   ↓ responder selects path
Service Request ASSIGNED
   ↓
FulfillmentAttempt(s)
   ↓ normalized provider/manual evidence
Service Request ACCEPTED / IN_PROGRESS / FULFILLED
   ↓ SUAS confirmation
CONFIRMED
```

Rules:

- `ProviderOffer` is not assignment or fulfillment.
- `FulfillmentAttempt` status is not Service Request status.
- A provider webhook cannot directly skip `ASSIGNED → ACCEPTED → IN_PROGRESS → FULFILLED` unless a later accepted contract explicitly adds a valid combined command preserving all prerequisites/events.
- `PROVIDER_COMPLETED` is evidence for a documented SUAS fulfillment command; it is not auto-confirmation.
- `PROVIDER_UNKNOWN` never means safe retry or fulfillment failure without reconciliation.
- Deliberate rematch/reroute creates a new Fulfillment Attempt identity where required by [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md).

---

## 6. Assignment concurrency

Provider/resource assignment is a contested mutation when multiple responders or jobs act concurrently.

- Assignment validates Case ownership and current Request state at write time.
- Two competing assignments from `MATCHING` cannot both become the canonical current assignment.
- The losing command conflicts and must not create a second logical `SERVICE_REQUEST_ASSIGNED` event.
- A later deliberate rematch is represented by the documented decline/escalate/cancel/re-match flow, not by silently overwriting provider identity.

Current-assignment projection (0.1.4): the current owner is the single `case_assignments` row with `status = ACTIVE`, and there is at most one `ACTIVE` assignment per Case ([DATA_MODEL.md](DATA_MODEL.md) §6). A Service Request has at most one `ServiceFulfillment`, which may have many `FulfillmentAttempt`s; an attempt with an ambiguous external outcome carries `PROVIDER_UNKNOWN` plus reconciliation bookkeeping and must reconcile before any duplicate-risk retry ([DATA_MODEL.md](DATA_MODEL.md) §7, [RESILIENCE.md](RESILIENCE.md)).

---

## 7. Categories

MVP: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`.

Reserved future: `BENEFITS`, `HOUSING`, `HEALTHCARE_NAVIGATION`, `COMMUNITY`, `OTHER`.

Unknown category codes are rejected.

---

## 8. Consent/privacy

Before a provider receives veteran data, SUAS evaluates the applicable consent/system basis at call time and sends only the minimum provider projection.

A prior assignment/offer does not create perpetual disclosure authorization. Reroutes/new provider disclosures re-evaluate the basis.

See [CONSENT.md](CONSENT.md), [PRIVACY.md](PRIVACY.md), [SECURITY.md](SECURITY.md).

---

## 9. Notifications

State changes may enqueue notifications only for recipients/channels with valid preferences and consent/system basis. Notification lag does not roll back committed request state.

Notification send retry does not replay the underlying Service Request command.

---

## 10. Expiry jobs

Expiry is durable asynchronous work in production.

- Exact TTL remains `DECISION_PENDING`.
- Duplicate expiry delivery is idempotent.
- Stale expiry jobs cannot expire a Request that has already moved beyond an allowed source state.
- Expiry job outage/delay is observable.

---

## 11. Non-goals

- hidden transitions;
- auto-fulfill on assignment;
- generative matching;
- Referral send as a Service Request transition;
- provider-native status as canonical request state;
- webhook bypass of authorization/confirmation;
- silent provider overwrite during reroute;
- non-atomic contested assignment.

---

## 12. Testability

Critical suite: **Service Request transition/concurrency**.

- every illegal edge fails;
- repeated command is idempotent;
- concurrent incompatible transitions yield one valid winner;
- competing assignment from `MATCHING` produces one canonical assignment/event;
- `ASSIGNED` is not `FULFILLED`;
- provider completion cannot auto-confirm;
- `PROVIDER_UNKNOWN` does not blindly retry/mutate request terminal state;
- confirm requires valid Fulfillment completion/confirmation evidence;
- duplicate/stale expiry jobs are harmless;
- provider disclosure is re-evaluated on reroute;
- cross-tenant/unauthorized provider actor cannot transition the request.
