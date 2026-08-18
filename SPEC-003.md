# SPEC-003.md — Check-In / Support Signal owner review worksheet (SUAS v0.1)

**Status:** `BLOCKED_BY_SPEC_001_AND_SPEC_002`  
**Lifecycle:** `draft` / `0.1.0`  
**Dependencies:** SPEC-001 accepted; SPEC-002 accepted  
**Specification owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-003  
**Required artifacts:** [CHECKINS.md](CHECKINS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [EVENT_MODEL.md](EVENT_MODEL.md)

This worksheet is preflighted specification work only. It must not be used to skip SPEC-001 or SPEC-002. Agents must not tick owner boxes or promote lifecycle.

---

## 1. Objective

Accept the versioned Check-In contract, deterministic Support Signal contract, and foundational event semantics required to make the SIGNAL stage reproducible, replay-safe, and non-clinical.

SPEC-003 does **not** decide D-011 scoring weights/thresholds and does not accept the later domain/data/architecture/provider/scale specs.

---

## 2. Governing invariants

Owner acceptance of SPEC-003 confirms these principles:

1. Check-In is an input artifact, not a Support Signal, Case, or Service Request.
2. Published QuestionnaireVersions are immutable and atomically visible to readers.
3. Completed Check-Ins are not silently rewritten.
4. Primary Support Signal computation is deterministic, inspectable, versioned, reproducible, and non-generative.
5. D-011 exact scoring remains `DECISION_PENDING` until separately closed/deferred for release.
6. Production signal computation is durable asynchronous work; duplicate job delivery must not create duplicate logical calculation results.
7. A stable computation identity separates retry/replay from deliberate new calculation.
8. Historical Support Signals are immutable; override creates a new linked row.
9. Required Domain Events are replay-safe and cannot be permanently lost after committed domain state.
10. `event_id`, command/job `idempotency_key`, `correlation_id`, and causal event identity are distinct concepts.
11. Infrastructure may deliver events/jobs at least once; consumers must provide exactly-once observable business effect where required.
12. Support Signal remains a coordination label, not diagnosis, suicidality score, or clinical assessment.

---

## 3. Owner checklist

All boxes remain unchecked until dependencies are accepted and the owner performs review.

### CHECKINS.md

- [ ] Check-In lifecycle `STARTED` → `IN_PROGRESS` → `COMPLETED` | `ABANDONED` | `INCOMPLETE` is correct.
- [ ] `CHECKIN_COMPLETED` is emitted only for the first logical completion.
- [ ] Repeated complete command does not duplicate the logical completion fact.
- [ ] Completed history is immutable; correction creates a new Check-In.
- [ ] Published QuestionnaireVersion is immutable and atomically visible.
- [ ] In-flight Check-In remains on its original questionnaire version.
- [ ] Incomplete input does not produce a production signal unless a published rule explicitly defines missing-input behavior.
- [ ] Completion may succeed before asynchronous signal settlement; missing/delayed settlement is operationally observable/recoverable.

### SUPPORT_SIGNALS.md

- [ ] Values remain exactly `GREEN`, `YELLOW`, `ORANGE`, `RED`.
- [ ] Primary computation is deterministic/versioned/inspectable/reproducible.
- [ ] No generative model produces the primary Support Signal.
- [ ] Check-In-derived primary computation identity uses Check-In + signal version + questionnaire version + primary kind, or equivalent stable key.
- [ ] Explicit-need signals require a stable source identity; null Check-In alone is insufficient.
- [ ] Duplicate/concurrent worker execution settles one logical authoritative primary result.
- [ ] New `signal_version` creates a new immutable calculation rather than rewriting history.
- [ ] Override creates a distinct immutable linked row with actor/reason.
- [ ] Duplicate replay does not emit a second logical `SUPPORT_SIGNAL_CHANGED` fact.
- [ ] Effective-signal selection must be deterministic and must not rely on insertion order alone.

### EVENT_MODEL.md

- [ ] `event_id` identifies a persisted fact and is not overloaded as command/job idempotency identity.
- [ ] `idempotency_key`, `correlation_id`, `causation_event_id`, and `request_id` semantics are acceptable.
- [ ] Domain state + required event use atomic/outbox/equivalent replay-safe publication semantics.
- [ ] Consumers tolerate duplicate delivery without duplicate domain effects.
- [ ] Global total event ordering is not required.
- [ ] Schema-version handling fails safely on incompatible versions.
- [ ] Event payload minimization prevents unnecessary sensitive free-text/provider payload duplication.
- [ ] Event catalog may grow additively during later draft stages only through explicit cross-spec reconciliation; vendor-native events are never canonical automatically.

### Cross-artifact

- [ ] CHECKINS, SUPPORT_SIGNALS, EVENT_MODEL use the same retry/replay semantics.
- [ ] DATA_MODEL draft can represent computation identity and event identity/idempotency separation without implying SPEC-006 acceptance.
- [ ] Safety/consent boundaries remain unchanged by signal infrastructure mechanics.
- [ ] The referenced MVP UI must not describe the Support Signal as a diagnosis/risk score.

---

## 4. D-011 boundary

D-011 remains `DECISION_PENDING` for exact Support Signal scoring rules and thresholds.

SPEC-003 may accept the **engine contract** without accepting production scoring content. If D-011 is still open at the release-decision stage, the owner must either:

- close D-011 with versioned rules/golden vectors; or
- explicitly defer production Support Signal computation with a non-ship/feature-unavailable boundary.

Unreleased fixtures must be labeled `UNRELEASED_FIXTURE` and cannot masquerade as a production `signal_version`.

---

## 5. Effective-signal projection boundary

The system needs a deterministic rule for selecting the effective coordination signal when multiple immutable primary/override rows exist.

SPEC-003 establishes only these constraints:

- insertion order alone is not authoritative;
- override linkage/history is preserved;
- lowering RED cannot erase prior safety actions/history;
- current/effective selection must be deterministic and auditable.

The exact projection/current-pointer/schema mechanism is reconciled under SPEC-006 with [DOMAIN_MODEL.md](DOMAIN_MODEL.md) and [DATA_MODEL.md](DATA_MODEL.md) before first release.

---

## 6. Test evidence required later

Before the CHECK-IN/SIGNAL implementation can claim conformance:

- questionnaire publication atomicity test;
- Check-In completion idempotency test;
- duplicate signal-job delivery/concurrency test;
- crash-after-domain-commit event publication recovery test;
- golden-vector test for every published `signal_version`;
- historical immutability test;
- override-chain/effective-selection tests after SPEC-006 defines the projection;
- test proving no generative primary signal path.

See [TESTING.md](TESTING.md).

---

## 7. Exit criteria

SPEC-003 can become owner-accepted only after SPEC-001 and SPEC-002 are accepted and the owner confirms the checklist.

Exit means:

- Check-In/versioning rules accepted;
- deterministic/replay-safe Support Signal engine contract accepted;
- event envelope/idempotency/replay semantics accepted;
- D-011 remains explicitly open or is separately decided;
- unresolved effective-signal data projection is handed to SPEC-006, not guessed in implementation.

**Implementation authority:** none until SPEC-016 release.

---

## 8. Non-goals

- closing D-011;
- choosing scoring weights/thresholds;
- clinical validation;
- accepting SPEC-004+;
- selecting a queue/broker/vendor;
- claiming exactly-once infrastructure delivery;
- allowing agents to self-accept.
