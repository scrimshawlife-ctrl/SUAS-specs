# SAFETY.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Safety posture
SUAS may surface and coordinate support situations, but it does not automate emergency response. Safety classification must remain deterministic, reviewable, and subject to human control.

## Support signal levels
- `GREEN`: routine status; no immediate coordination trigger.
- `YELLOW`: emerging need or minor instability; coordination may be scheduled.
- `ORANGE`: urgent support need requiring prompt human review and coordination.
- `RED`: highest non-automated escalation level requiring immediate human attention under defined operational procedure.

## Human-in-the-loop requirements
- A human must review `RED` handling actions.
- A human must approve any escalation to external emergency pathways.
- AI cannot make safety-critical decisions.
- Generative AI must not originate the primary support signal.

## No automated 911 rule
No workflow in SUAS may dial, text, dispatch, or otherwise automatically contact 911 or any emergency dispatch service. The system may present human guidance or logging workflows for authorized staff, but the triggering action must remain manual and outside automated dispatch behavior.

## Escalation paths
- `GREEN`: log, monitor, continue scheduled follow-up.
- `YELLOW`: route to coordinator queue and evaluate service requests.
- `ORANGE`: prioritize coordinator review, create assignments, consider direct veteran contact if consent permits.
- `RED`: immediate human review, documented decision, manual outreach and manual escalation path if warranted.

## Evidence and audit
Every safety-relevant transition should record reason codes, actor identity, timestamp, and whether the change was system-derived or human-overridden. Human overrides are `[OBSERVED]`; rule outputs are `[INFERRED]`.

## Non-goals
- No crisis prediction.
- No diagnosis.
- No automated triage using opaque AI judgments.

Related specs: [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [CHECKINS.md](CHECKINS.md), [DISPATCH.md](DISPATCH.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md), [AGENTS.md](AGENTS.md).
