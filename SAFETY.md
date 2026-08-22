# SAFETY.md — Red-state behavior and safety non-goals (SUAS v0.1)

**Related:** [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [CONSENT.md](CONSENT.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [ARCHITECTURE.md](ARCHITECTURE.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [PRODUCT.md](PRODUCT.md), [FRICTION.md](FRICTION.md)

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

Approved on-screen safety copy is released by **D-012** in [SAFETY_COPY.md](SAFETY_COPY.md) (v0.1.5). Implementations render that approved copy where this document requires it and MUST NOT invent alternative crisis-resource wording presented as official. The only authorized crisis destinations are **911** and the **988 Suicide & Crisis Lifeline** (call or text; Veterans reach the Veterans Crisis Line through 988). Closing D-012 approves copy only; it is not production-operating approval (SPEC-018 still gates real operation).

---

## 3. Red-state behavior (`RED` effective Support Signal)

When the effective signal is `RED`, the system **must**:

### 3.1 Surface approved crisis resources

- Show the veteran the **approved** crisis-resource copy and destinations from [SAFETY_COPY.md](SAFETY_COPY.md) (D-012): 911 for immediate danger and 988 (call or text) for suicidal thoughts / emotional crisis.
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

If a Veteran indicates they need emergency services, the UI displays the approved copy in [SAFETY_COPY.md](SAFETY_COPY.md) instructing them to contact 911 / 988 themselves. SUAS still does not auto-dispatch.

### 5.1 State truthfulness (D-012)

Crisis and practical-support surfaces MUST obey the core principle in [SAFETY_COPY.md](SAFETY_COPY.md) §5: never let interface language imply a stronger real-world intervention than the system can prove occurred. The conditions `REQUESTED ≠ ACCEPTED ≠ DISPATCHED ≠ ARRIVED ≠ RESOLVED` are distinct, are part of the domain model (mapped onto the canonical Service Request / Fulfillment states and the [MVP_REFERENCE.md](MVP_REFERENCE.md) §7.2 truthfulness table), and a later state is surfaced only when its recorded fact exists. `DISPATCHED`/`ARRIVED` describe a verified **practical-support** provider only and never emergency-services dispatch. The approved/forbidden phrase lists in [SAFETY_COPY.md](SAFETY_COPY.md) §4 bind every crisis surface.

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
- Inventing safety copy or crisis destinations outside the D-012 approved set in [SAFETY_COPY.md](SAFETY_COPY.md)
- Interface language implying a stronger intervention than the recorded facts prove (SAFETY_COPY.md §5)

---

## 9. Testability

Critical suite: **red-state behavior** ([TESTING.md](TESTING.md) SAFETY gate).

- `RED` surfaces the approved D-012 crisis copy/destinations from [SAFETY_COPY.md](SAFETY_COPY.md) (911 and 988) and does not call an emergency API.
- Human review queue priority is set.
- Trusted Contact is notified only with `can_receive`+`RED`.
- Revoked grant blocks the alert.
- No generative function in the red-state decision path.
- Copy does not claim SUAS is emergency services, ships no crisis destination other than 911/988, and never displays a support state (`ACCEPTED`/`DISPATCHED`/`ARRIVED`/`RESOLVED`) whose recorded fact does not exist (SAFETY_COPY.md §4, §5).
