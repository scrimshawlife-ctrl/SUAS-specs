# ANALYTICS.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Analytics scope
Analytics should measure pilot operations, service throughput, and follow-up outcomes using aggregate and privacy-preserving methods.

## Measurement principles
- prefer aggregate counts and rates
- minimize personally identifying detail
- separate operational dashboards from research claims
- retain provenance for metric definitions

## Candidate metrics
- cases opened by week
- service requests by category and state
- time from `SIGNAL` to first human action
- fulfillment rate by service category
- follow-up completion rate
- reopen rate after `FOLLOWUP` or `RESOLVED`

## Aggregation rules
Metrics should be computed from canonical domain events and state transitions. Individual-level outputs for staff use may exist operationally, but analytics reporting should avoid re-identification when shared broadly.

## Privacy rule
No analytics export should reveal more data than necessary. De-identification and suppression rules are `DECISION_PENDING` under `ADR-024` from [PRIVACY.md](PRIVACY.md).

## Speculative areas
- [SPECULATIVE] satisfaction scoring model
- [SPECULATIVE] cohort comparison across service categories
- [SPECULATIVE] long-term stability metrics beyond pilot period

## Non-goals
- no predictive risk scoring
- no unsupported claims of efficacy
- no public dashboards with identifiable veteran data

Related specs: [EVENT_MODEL.md](EVENT_MODEL.md), [PRIVACY.md](PRIVACY.md), [PILOT.md](PILOT.md), [VERSIONING.md](VERSIONING.md).
