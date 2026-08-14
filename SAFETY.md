# SAFETY.md — Red-state behavior and safety non-goals (SUAS v0.1)

**Related:** [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [CONSENT.md](CONSENT.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [ARCHITECTURE.md](ARCHITECTURE.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [PRODUCT.md](PRODUCT.md)

**Actors:** Veteran, Responder, Trusted Contact (only if granted), SUAS System Administrator, System.

---

## 1. Purpose

Define what SUAS does — and must not do — when the effective Support Signal is `RED`, and the standing safety rules for all states.

SUAS is a coordination platform. It does not replace emergency services.

---

## 2. Standing non-goals (all states)

The product and implementation **must not**:

1. Perform automated emergency dispatch (including automated 911 / public-safety calling).
2. Diagnose a medical or psychiatric condition.
3. Claim to predict or determine suicidality.
4. Perform autonomous emergency intervention (no unattended action that claims to "handle" an emergency).
5. Imply that SUAS replaces emergency services.
6. Use a generative model for any safety-critical decision (see §6).

Approved on-screen safety copy is **D-012 `DECISION_PENDING`**. Until D-012 is closed, implementation may reserve a copy slot but must not invent crisis-resource wording presented as official.

---

## 3. Red-state behavior (`RED` effective Support Signal)

When the effective signal is `RED`, the system **must**:

### 3.1 Surface approved crisis resources

- Show the veteran the **approved** crisis-resource list (copy and destinations from D-012 when closed).
- This is a display of resources, not a dispatch.
- Do not auto-dial, auto-SMS public safety, or auto-create an emergency ticket at an external agency.

### 3.2 Prioritize human review

- Open or update a Support Case with `priority_signal_level=RED` if one is not already open for this veteran in an active coordination state (see [CASES.md](CASES.md)).
- Place the case at the front of the responder queue filter for red-state ([RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md)).
- A human Responder performs review. The system does not close, downgrade, or "resolve" red-state without a human command.

### 3.3 Follow consent for Trusted Contacts

- Notify a Trusted Contact **only** if an `ACTIVE` Consent Grant exists: `can_receive` + `RED` (or a grant that explicitly includes `RED`).
- Do not notify on membership alone.
- Do not escalate to contacts who lack the grant.
- Re-check the grant immediately before send ([CONSENT.md](CONSENT.md), [NOTIFICATIONS.md](NOTIFICATIONS.md)).

### 3.4 Audit

Emit and persist:

- Domain: `SUPPORT_SIGNAL_CHANGED` (if the level changed), `CASE_CREATED` or case update, `TRUSTED_CONTACT_ALERTED` only if a send actually occurred
- Audit: red-state UI surfaced, queue prioritized, each notify allow/deny, any override

---

## 4. Yellow and orange

`YELLOW` and `ORANGE` use the same consent rules (`can_receive` + that exact level) and may open or update a Support Case per [CASES.md](CASES.md). They do not trigger red-state crisis-resource surfacing unless D-012 later says a subset of that copy is also shown (not specified now = do not show).

---

## 5. Veteran-initiated emergency

If a Veteran indicates they need emergency services, the UI may display approved copy (D-012) instructing them to contact local emergency services themselves. SUAS still does not auto-dispatch.

---

## 6. AI policy

**No generative AI for safety-critical decisions.**

Must not be determined by a generative model:

- primary Support Signal
- suicidality or any clinical determination
- emergency intervention
- whether to notify a Trusted Contact
- service qualification
- case closure

Future assistive uses (not MVP; require a later spec): resource deduplication, note summarization, categorization, admin search, aggregate analytics. Assistive output is advisory and must not write safety-critical state without a human command.

See [ARCHITECTURE.md](ARCHITECTURE.md).

---

## 7. Events

Listed in §3.4. Notification records must include `consent_basis` ([NOTIFICATIONS.md](NOTIFICATIONS.md)).

---

## 8. Non-goals (restated)

- Automated 911
- Diagnosis
- Suicide-prediction claim
- Autonomous emergency intervention
- Replacing emergency services
- Invented safety copy before D-012

---

## 9. Testability

Critical suite: **red-state behavior** ([TESTING.md](TESTING.md) SAFETY gate).

- `RED` surfaces the approved-resource slot (or a placeholder when D-012 is open) and does not call an emergency API.
- Human review queue priority is set.
- Trusted Contact is notified only with `can_receive`+`RED`.
- Revoked grant blocks the alert.
- No generative function in the red-state decision path.
- Copy does not claim SUAS is emergency services.
