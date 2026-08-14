# CHECKINS.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Purpose
A `CheckIn` is the most direct structured input into the support coordination loop. It records a veteran-submitted or staff-assisted status update that may inform a `SupportSignal`.

## Check-in flow
1. Veteran or authorized staff opens a check-in channel.
2. The system presents concise, non-clinical prompts.
3. Responses are recorded as `[OBSERVED]` data.
4. Deterministic rules compute a support signal.
5. A coordinator reviews resulting needs when escalation thresholds are met.

## Frequency
- [SPECULATIVE] Daily or weekly cadence may be appropriate depending on pilot operations.
- Frequency policy remains configurable and should avoid coercive or excessive prompting.
- Missed check-ins alone should not imply a clinical or safety conclusion.

## Candidate fields
- submission timestamp
- channel used
- self-reported current status
- support categories needed
- time sensitivity
- optional location or pickup context
- optional trusted-circle outreach request
- optional free-text note

## Rules
- Check-ins must not ask for diagnoses.
- Check-ins must not create emergency dispatch automatically.
- Generative AI must not rewrite the primary meaning of check-in content before signal computation.
- Human assistance with entry must preserve provenance.

## Relationship to signals
A check-in can produce zero or one primary `SupportSignal`. Reason codes and level assignment logic are defined in [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md).

## Audit expectations
The system should retain submission provenance, rule version, and any human override. Veteran-entered text remains `[OBSERVED]`; the assigned signal level is `[INFERRED]` unless a human changes it.

Related specs: [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [CONSENT.md](CONSENT.md), [SAFETY.md](SAFETY.md), [DOMAIN_MODEL.md](DOMAIN_MODEL.md).
