# AGENTS.md — Required rules for agents and implementers

**Released stack:** `0.1.1`  
**Implementation authority:** `RELEASED_FOR_IMPLEMENTATION`  
**Current stage:** `SPEC-017`  
**Start here:** [HANDOFF.md](HANDOFF.md), [ENVIRONMENT.md](ENVIRONMENT.md), [RELEASE_MANIFEST-0.1.1.md](RELEASE_MANIFEST-0.1.1.md)

This file binds human and automated agents working on SUAS specifications or implementation.

## Required rules

1. `SUAS-specs` is canonical. `SUAS` conforms to released specs.
2. Implementation PRs cite released spec file/section, stack version, release manifest, and applicable test/readiness contract.
3. Semantic gaps return to specs. Do not invent product/domain rules in code.
4. Deployment, configuration, vendor choice, runtime flags, and provider behavior cannot redefine canonical semantics.
5. Follow [ENVIRONMENT.md](ENVIRONMENT.md). Invalid environment/feature combinations fail closed; LOCAL/TEST/STAGING must not contact real veterans/providers or use production data.
6. Do not guess deferred production decisions or external facts.
7. No safety-critical generative AI and no automated emergency dispatch.
8. Match canonical terminology/state/event names exactly.
9. Preserve [MVP_REFERENCE.md](MVP_REFERENCE.md), including required truthful/safe divergences.
10. Preserve provider neutrality. Provider SDKs/payloads/statuses remain adapter-local; Manual/Fake adapters are first-class during current implementation.
11. Preserve stateless/shared correctness state, durable async-work semantics, persistent idempotency, tenant isolation, replay-safe events, and bounded access paths.
12. No real production-unavailable surface may be enabled merely through configuration or implementation default.
13. Every shared build exposes app commit/version, spec version, release manifest, environment, and schema/migration version where applicable.
14. Do not commit secrets, `.env`, production data, provider credentials, real contact details, or copied production payloads.

## Cross-repo governance

| Rule | `SUAS-specs` | `SUAS` |
|---|---|---|
| Authority | canonical released contract | implementation/conformance |
| Product gaps | specified/released here | returned to specs |
| Vendors | capability boundaries + decision records | adapters only when release permits |
| Environments | `ENVIRONMENT.md` contract | validated configuration implementation |
| Versioning | stack/release/runtime authority | app/schema/build versions mapped to released stack |
| Undocumented behavior | not canonical | not canonical |

## Current release boundary

v0.1.1 authorizes implementation, not production operation. Pilot and production readiness remain `NOT_READY`.

Still unavailable for production include real infrastructure/provider effects, production Support Signal scoring, official safety copy, real veteran data/live pilot operation, production workload/SLO/RTO/RPO claims, and sensitive aggregate reporting unless superseded by a later released decision/evidence set.

## Epistemic discipline

Use `OBSERVED`, `INFERRED`, `SPECULATIVE`, `NOT_COMPUTABLE`, `DECISION_PENDING`, and `FUTURE` accurately. Prototype behavior, a vendor API, or code behavior is not evidence of a product decision.

## Repository boundary

`SUAS-specs` contains specification/governance/handoff material, not application code or credentials. `SUAS` contains implementation. Fable and other implementers should follow [HANDOFF.md](HANDOFF.md) before the first change.