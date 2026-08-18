# SPEC-003.md — Owner acceptance record: Check-In, Support Signal, events

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the versioned Check-In contract, deterministic/replay-safe Support Signal engine contract, and foundational event semantics.

Accepted invariants:

- Check-In is an input artifact and published QuestionnaireVersions are immutable.
- Completed Check-Ins are not silently rewritten.
- Primary Support Signal computation is deterministic, inspectable, versioned, reproducible, non-generative, and idempotently settled under duplicate/concurrent work.
- Historical Support Signals are immutable; overrides create linked immutable rows.
- `event_id`, command/job idempotency, correlation, causation, and request identity are distinct.
- Required Domain Event publication is replay-safe and cannot be permanently lost after committed state.
- At-least-once infrastructure delivery is allowed; exactly-once observable business effect is required where consequential.
- Support Signal remains a coordination label, not a diagnosis or suicidality determination.

D-011 production scoring remains deferred for v0.1.0; production Support Signal scoring is `UNAVAILABLE` under the release decision ledger. The deterministic current/effective projection defined in SPEC-006 is accepted as the implementation target.
