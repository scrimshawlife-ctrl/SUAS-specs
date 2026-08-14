# SUPPORT_SIGNALS.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Signal model
`SupportSignal` is the deterministic output of defined rules over check-ins and verified operational observations. It is not a generative prediction system.

## Allowed levels
- `GREEN`
- `YELLOW`
- `ORANGE`
- `RED`

## Deterministic computation rules
Signal logic should use explicit reason codes such as:
- requested food support within normal scheduling window
- transportation request with same-day timing
- temporary shelter need without confirmed placement
- direct statement requesting immediate human contact
- failed fulfillment with worsening need

Each reason code must map to a defined level outcome and be stored for audit. [INFERRED] Composite rules may choose the highest applicable level when multiple reason codes fire.

## Level definitions
- `GREEN`: no urgent coordination indicators; monitor and proceed with normal workflow.
- `YELLOW`: support need present but manageable through normal queueing.
- `ORANGE`: urgent operational need requiring prompt human review.
- `RED`: highest level requiring immediate human attention and manual decision-making.

## Escalation triggers
Examples include:
- unmet same-day shelter need -> at least `ORANGE`
- explicit immediate help request -> `ORANGE` or `RED` per policy
- repeated failed outreach with worsening observed conditions -> policy-defined escalation
- coordinator manual override with documented reason -> allowed and auditable

## Prohibitions
- No generative AI in the primary signal path.
- No unsupported psychological, psychiatric, or medical inference.
- No automated emergency dispatch from any signal level.

## Governance
Rule changes require versioning, review, and audit traceability. `ADR-025` covers signal rule governance and deployment control.

Related specs: [CHECKINS.md](CHECKINS.md), [SAFETY.md](SAFETY.md), [CASES.md](CASES.md), [DECISIONS.md](DECISIONS.md).
