# CHECKINS.md — Versioned questionnaires (SUAS v0.1)

**Related:** [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md), [DATA_MODEL.md](DATA_MODEL.md), [PRODUCT.md](PRODUCT.md), [PRIVACY.md](PRIVACY.md)

**Actors:** Veteran (respondent), SUAS System Administrator (publisher), Responder (reader only with basis).

---

## 1. Purpose

A Check-In is a Veteran's completion (or partial completion) of a published questionnaire. It is an **input artifact**. It is not a Support Signal, not a Support Case, and not a Service Request.

---

## 2. Entities

| Entity | Role |
|---|---|
| `QuestionnaireVersion` | Immutable published questionnaire (`qv-*`) |
| `Question` | Item on a version; may declare a dimension |
| `AnswerOption` | Closed-choice option |
| `CheckIn` | One attempt by a Veteran |
| `CheckInResponse` | One answer on that attempt |

---

## 3. Dimensions (may include)

A published version **may** include questions in these dimensions:

`sleep`, `connection`, `stress`, `basic_needs`, `coping`, `safety`

Exact questions, option weights, and required/optional flags are `NOT_COMPUTABLE` until a QuestionnaireVersion is published. Do not invent clinical instruments. Do not claim validated psychometrics.

---

## 4. Check-In states

`STARTED` → `IN_PROGRESS` → `COMPLETED` | `ABANDONED` | `INCOMPLETE`

| State | Meaning |
|---|---|
| `STARTED` | Record created; no required answers yet |
| `IN_PROGRESS` | At least one response saved |
| `COMPLETED` | All required questions answered; submitted |
| `INCOMPLETE` | Submitted or timed out with required questions missing; explicitly marked |
| `ABANDONED` | Veteran or system marked abandoned (idle timeout) |

### 4.1 Incomplete

An `INCOMPLETE` Check-In may still produce a Support Signal only if the published `signal_version` defines a rule for missing inputs. If it does not, no signal is computed; a Domain Event is not faked. Default until D-011 is closed: **do not compute a signal from incomplete input** (`INFERRED` from determinism + do-not-guess).

### 4.2 Abandoned

Idle timeout value `DECISION_PENDING`. Abandoned check-ins remain stored. They are not silently deleted. They do not open a Support Case by themselves.

### 4.3 Edit

Completed Check-Ins are not silently rewritten. A veteran correction creates a **new** Check-In (and a new signal computation). Responders cannot edit answers.

### 4.4 Timing

Record `started_at`, `completed_at` / `abandoned_at`. Do not use client clock as the sole authority; server time wins.

### 4.5 Migration

When a new QuestionnaireVersion is published, in-flight Check-Ins continue on their original version. New Check-Ins use the current `PUBLISHED` version. Historical rows keep `questionnaire_version`. There is no in-place rewrite of old questions.

---

## 5. Publication

SUAS-admin: `DRAFT` → `PUBLISHED` → `SUPERSEDED`. Published versions are immutable. See [ADMIN.md](ADMIN.md), [VERSIONING.md](VERSIONING.md).

---

## 6. Events

- Domain: `CHECKIN_COMPLETED` (only on `COMPLETED`)
- Audit: start, save, complete, abandon, admin publish

Completion triggers the Support Signal job ([ARCHITECTURE.md](ARCHITECTURE.md)).

---

## 7. Authorization

- Veteran: create/update own in-progress Check-In; read own history.
- Responder: read answers only with `can_view` + `checkin_answers` or the documented assigned-responder basis in [CONSENT.md](CONSENT.md).
- Trusted Contact: only with an explicit `can_view` + `checkin_answers` grant. Membership is insufficient.

---

## 8. Non-goals

- Diagnosis
- Continuous passive telemetry as a Check-In
- Import of third-party clinical assessments
- Free-text that solicits medical history or SSN ([PRIVACY.md](PRIVACY.md))

---

## 9. Testability

- Version immutability after publish
- In-flight check-in stays on old version after publish
- Completed cannot be silently edited
- Abandoned ≠ Completed
- Incomplete does not emit `CHECKIN_COMPLETED`
- CHECK-IN gate in [TESTING.md](TESTING.md) and [STATUS.md](STATUS.md)
