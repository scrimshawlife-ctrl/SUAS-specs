# SPEC-004.md — Owner acceptance record: Cases, Service Requests, responder workflow

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the canonical Support Case and Service Request state machines and responder/QRF operating actions.

Accepted invariants:

- Support Case, Service Request, Referral, Assignment, Fulfillment Attempt, and Fulfillment remain distinct.
- State transitions are explicit command-driven operations; hidden status mutation is forbidden.
- Contested Case creation/claim/assignment/reassignment and incompatible Request transitions use mutation-time atomic one-winner semantics.
- Queue/browser state is advisory; committed server state is authoritative.
- Duplicate commands/jobs do not duplicate logical effects.
- Provider callbacks/status are evidence only and cannot bypass consent, authorization, canonical transitions, fulfillment, or confirmation.
- Responder/QRF interaction remains fast, action-first, mobile-usable, and non-EHR.
- Cross-tenant queue/action access is forbidden.
- Generative AI does not claim, assign, resolve, close, or qualify service requests.

The logical data representations reconciled in SPEC-006 are accepted as the implementation target.
