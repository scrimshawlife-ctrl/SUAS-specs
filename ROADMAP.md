# ROADMAP.md
spec_version: 0.1.0
status: DRAFT
phase: SPECIFICATION_BOOTSTRAP

## Roadmap overview
This roadmap defines the specification workstream from bootstrap through pilot-readiness review. Each item may produce revisions across multiple files.

| Spec ID | Objective | Dependencies | Exit criteria |
| --- | --- | --- | --- |
| SPEC-001 | Finalize product scope and domain vocabulary | Bootstrap docs | Product boundaries, loop, and glossary reviewed |
| SPEC-002 | Resolve consent taxonomy | SPEC-001 | Grant names, scope rules, and revocation rules approved |
| SPEC-003 | Finalize safety signal rules | SPEC-001, SPEC-002 | Deterministic reason codes and override policy approved |
| SPEC-004 | Finalize case and request state semantics | SPEC-001, SPEC-003 | State transitions and ownership rules approved |
| SPEC-005 | Finalize resource and referral model | SPEC-001, SPEC-004 | Matching and referral data rules approved |
| SPEC-006 | Finalize fulfillment, follow-up, and settlement logic | SPEC-004, SPEC-005 | Closure criteria and confirmation policy approved |
| SPEC-007 | Resolve authentication and authorization design | SPEC-002 | Identity assurance and access model ADRs decided |
| SPEC-008 | Resolve privacy and security baseline | SPEC-002, SPEC-007 | Retention, encryption, audit, and HIPAA applicability review completed |
| SPEC-009 | Finalize notification policy | SPEC-002, SPEC-007 | Channel rules, provider decision, and quiet-hours policy approved |
| SPEC-010 | Finalize API and event model | SPEC-004, SPEC-007 | API versioning, event catalog, and concurrency rules approved |
| SPEC-011 | Finalize architecture and deployment decisions | SPEC-007, SPEC-010 | Hosting, runtime, jobs, and packaging ADRs approved |
| SPEC-012 | Finalize operations and incident response | SPEC-008, SPEC-011 | Observability, on-call, and incident workflows approved |
| SPEC-013 | Finalize analytics and pilot metrics | SPEC-006, SPEC-008 | Aggregate metric definitions and de-identification rules approved |
| SPEC-014 | Conduct cross-spec review and gap closure | SPEC-001 through SPEC-013 | Open contradictions resolved and statuses updated |
| SPEC-015 | Pilot readiness specification review | SPEC-014 | Implementation authority recommendation prepared |

## Exit discipline
No roadmap item is complete until affected files are updated, cross-references are checked, and unresolved decisions are either decided or explicitly carried forward.

## Current posture
All items remain future work beyond bootstrap. [INFERRED] The recommended next run is focused review toward `SPEC-001`, not implementation.

Related specs: [STATUS.md](STATUS.md), [SPEC_AUDIT.md](SPEC_AUDIT.md), [DECISIONS.md](DECISIONS.md), [PILOT.md](PILOT.md).
