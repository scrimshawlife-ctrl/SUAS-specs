# AGENTS.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Agent and automation scope
Automation in SUAS may assist with data entry support, routing suggestions, reminders, and operational summaries, but it must remain bounded by human authority and consent rules.

## Allowed automation roles
- deterministic support-signal computation
- notification scheduling
- queue sorting based on explicit rules
- draft summarization for non-safety-critical notes `[DECISION_PENDING tooling under ADR-046]`
- analytics aggregation on de-identified data

## Forbidden automation roles
- autonomous safety decisions
- automated 911 dispatch
- generative AI as primary signal source
- diagnosis or clinical recommendation
- unreviewed partner eligibility determination

## Human authority
Coordinators, responders, peer supporters, and admins remain accountable for operational decisions. AI outputs must be reviewable, rejectable, and clearly marked as machine-generated when used.

## Decision-pending items
- `ADR-046`: approved AI tooling boundaries.
- `ADR-047`: machine-generated summary retention and disclosure policy.

## Audit note
Any use of automation that affects user-visible actions or coordination priorities should emit audit traces.

Related specs: [SAFETY.md](SAFETY.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md), [DECISIONS.md](DECISIONS.md).
