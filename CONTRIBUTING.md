# CONTRIBUTING.md — How to change SUAS specifications

**Related:** [AGENTS.md](AGENTS.md), [VERSIONING.md](VERSIONING.md), [CHANGELOG.md](CHANGELOG.md), [README.md](README.md), [ROADMAP.md](ROADMAP.md)

This repository contains specifications and hygiene files only. Do not add application or production code.

---

## 1. Authority

- Specification owner: `@scrimshawlife-ctrl`.
- Contributors and implementation agents may propose changes but cannot self-accept or self-release them.
- Implementation gaps return to this repository; code does not redefine the product.

---

## 2. Change types

| Type | Examples | Typical version impact |
|---|---|---|
| Clarification | wording/cross-link with no behavior change | PATCH |
| Additive contract | optional field, new readiness rule, new future capability | MINOR |
| Breaking contract | state rename, required field, consent semantic change | MAJOR, or explicit breaking 0.y MINOR while unreleased |
| Decision close | resolving a DECISION_PENDING item | MINOR or MAJOR depending on behavior impact |

---

## 3. Required process

1. Read [PRODUCT.md](PRODUCT.md) and [GLOSSARY.md](GLOSSARY.md).
2. Identify the governing [ROADMAP.md](ROADMAP.md) stage.
3. Edit the minimum coherent set of files; do not patch one contract while leaving dependent artifacts contradictory.
4. Reconcile cross-links and terminology.
5. Update [DECISIONS.md](DECISIONS.md) when opening/closing a decision.
6. Update [STATUS.md](STATUS.md) only when status/gates actually change.
7. Update [CHANGELOG.md](CHANGELOG.md) for semantic spec changes.
8. Add or update testability/acceptance criteria for new behavior.
9. Do not invent partners, provider capabilities, contracts, county agreements, VA integrations, Medi-Cal eligibility, responder coverage, legal status, capacity forecasts, SLOs, RTO/RPO, or vendor SLAs.
10. Do not claim HIPAA compliance.
11. Keep application implementation in `scrimshawlife-ctrl/SUAS` and cite released specs there.

---

## 4. Cross-artifact conformance review

Before a spec change is ready for owner review, check all affected authority surfaces.

At minimum, when relevant:

- product/terminology: `PRODUCT.md`, `GLOSSARY.md`, `README.md`;
- domain/state: `DOMAIN_MODEL.md`, `DATA_MODEL.md`, `API.md`, `EVENT_MODEL.md`;
- visual/interaction: `MVP_REFERENCE.md`;
- external fulfillment: `PROVIDER_INTEGRATIONS.md`, `APIS.md`, `FULFILLMENT.md`;
- scale/resilience: `SCALING.md`, `RESILIENCE.md`, `ARCHITECTURE.md`, `DEPLOYMENT.md`, `OPERATIONS.md`;
- verification: `TESTING.md`, `STATUS.md`;
- governance: `ROADMAP.md`, `SPEC-001.md`, `VERSIONING.md`, `AGENTS.md`, `SPEC_AUDIT.md`.

A change that leaves a stale gate name, roadmap number, lifecycle claim, provider assumption, or state alias is incomplete.

---

## 5. Review bar

A specification change is reviewable when:

- terminology matches canonical definitions;
- state names and event semantics align across artifacts;
- provider-specific details do not leak into domain contracts;
- MVP-facing changes preserve or deliberately document visual/interaction divergence;
- scale/resilience implications are addressed where behavior can create load, concurrency, or dependency-failure risk;
- testability is explicit;
- unknowns remain epistemically labeled;
- lifecycle/roadmap references are current;
- safety/non-goal boundaries remain intact.

---

## 6. Not allowed here

- application code;
- provider credentials;
- real veteran data;
- vendor-specific production deployment implementation;
- clinical protocols presented as product claims;
- silent acceptance/release changes.
