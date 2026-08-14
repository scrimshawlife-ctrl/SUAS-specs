# SUPPORT_SIGNALS.md — Deterministic coordination signals (SUAS v0.1)

**Related:** [CHECKINS.md](CHECKINS.md), [SAFETY.md](SAFETY.md), [CASES.md](CASES.md), [CONSENT.md](CONSENT.md), [TESTING.md](TESTING.md), [DECISIONS.md](DECISIONS.md)

**Actors:** System (compute), Veteran (source of Check-In), Responder (may override with reason), SUAS System Administrator (publishes `signal_version`).

---

## 1. Purpose

A Support Signal is a **coordination priority label**, not a diagnosis, not a clinical assessment, and not a suicidality determination.

Values (exactly these four):

`GREEN` | `YELLOW` | `ORANGE` | `RED`

---

## 2. Computation contract

The primary signal **must** be:

| Property | Requirement |
|---|---|
| Deterministic | Same inputs + same `signal_version` + same `input_questionnaire_version` → same `level` and same `basis` |
| Inspectable | `basis` records the inputs used |
| Versioned | `signal_version` is a published identifier |
| Unit tested | Golden vectors per version ([TESTING.md](TESTING.md)) |
| Reproducible | Historical rows can be explained without re-mutating them |

**No generative model may produce the primary signal.**

Exact scoring rules and thresholds are **D-011 `DECISION_PENDING`**. Implementation may build a pure function interface and fixtures, but must not ship invented weights as if decided.

---

## 3. Recorded fields (required)

- `signal_version`
- `input_questionnaire_version`
- `computed_at`
- `basis`
- `level`
- `veteran_profile_id`
- `check_in_id` (nullable only for an explicit need that still records why no check-in exists)

---

## 4. Historical integrity

**No silent mutation of historical calculations.**

- A new `signal_version` does not rewrite old rows.
- If a recomputation is ever performed, write a **new** `SupportSignal` row.
- Overrides write a new row with `override_of_signal_id`, `override_actor_id`, `override_reason`. The original remains.

---

## 5. Override policy

- Who: assigned Responder or SUAS-admin.
- When: documented disagreement with the computed label for coordination purposes (for example, veteran contacted the responder directly with a different need urgency).
- Required: reason text, actor, timestamp, link to original.
- Override is not a diagnosis. It does not delete the computed signal.
- Red-state behavior in [SAFETY.md](SAFETY.md) still applies to the **effective** (latest non-superseded) signal used for coordination. If an override lowers a `RED`, the act is audited and requires a reason; it does not remove already-surfaced crisis-resource UI from the veteran's current session (`INFERRED` safety posture). Lowering `RED` via override is allowed only as a coordination label change, never as a claim that risk is gone.

---

## 6. Relationship to the loop

SIGNAL is the first stage of the canonical loop ([PRODUCT.md](PRODUCT.md)). A `YELLOW`/`ORANGE`/`RED` effective signal is a reason to open or update a Support Case; it is not itself a Service Request.

Case creation rules: see [CASES.md](CASES.md). Signal change emits `SUPPORT_SIGNAL_CHANGED`.

---

## 7. Visibility

Per [CONSENT.md](CONSENT.md): `can_view` + `support_signal`. Trusted Circle membership is insufficient. Notifications at a level require `can_receive` + that level.

---

## 8. Non-goals

- Diagnosis
- Suicide prediction or suicidality scoring
- Generative "interpretation" of free text as the primary signal
- Mutating yesterday's signal because today's rules changed

---

## 9. Testability

Critical suite: **support-signal determinism**.

- Golden vectors: fixed inputs → fixed level + basis
- Version change does not alter old rows
- Override creates a new row
- No generative path in the primary compute function
