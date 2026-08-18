# AGENTS.md — Required rules for agents and implementers

**SPEC-001 status:** `READY_FOR_REVIEW` (not `accepted`; not `released`)  
**Related:** [README.md](README.md), [CONTRIBUTING.md](CONTRIBUTING.md), [VERSIONING.md](VERSIONING.md), [STATUS.md](STATUS.md), [ROADMAP.md](ROADMAP.md), [SPEC-001.md](SPEC-001.md)

This file binds human and automated agents working on SUAS specifications or the SUAS implementation.

---

## 1. Required rules

1. **Specs are authority.** `SUAS-specs` is canonical. `SUAS` must conform to released specs.
2. **Cite contracts.** Implementation PRs cite spec file, section, stack version, lifecycle, relevant artifact versions, and applicable readiness contract.
3. **Gaps return to specs.** Do not invent unstated product/domain rules in implementation.
4. **Deploy does not redefine product semantics.** Vendor choice, topology, runtime flags, and scaling mechanism must not change canonical domain meaning.
5. **Undocumented implementation is not canonical.** Code behavior is not a product rule until specified and released.
6. **Do not guess open decisions.** Follow [DECISIONS.md](DECISIONS.md); use `DECISION_PENDING` / `NOT_COMPUTABLE`.
7. **Do not invent external facts.** No invented partner capabilities, county agreements, VA integrations, Medi-Cal eligibility, provider coverage, funding, staffing, legal status, SLAs, or adoption forecasts.
8. **No safety-critical generative AI.** No generative primary Support Signal, suicidality determination, emergency intervention, trusted-contact notify decision, service qualification, or case closure.
9. **No automated emergency dispatch.** SUAS does not implement 911/PSAP auto-dispatch or claim to replace emergency services.
10. **Match canonical names exactly.** State-machine names, event types, role names, and glossary terms must match released contracts.
11. **Preserve the MVP reference.** UI work must follow [MVP_REFERENCE.md](MVP_REFERENCE.md); visual drift is not an implementation preference.
12. **Preserve provider neutrality.** Domain code must not import provider SDKs or expose vendor statuses as SUAS states. Follow [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md).
13. **Do not design to pilot-only ceilings.** Follow [SCALING.md](SCALING.md) and [RESILIENCE.md](RESILIENCE.md); keep the modular monolith simple but stateless/durable where required.

---

## 2. Cross-repo governance

| Rule | `SUAS-specs` | `SUAS` |
|---|---|---|
| Authority | Canonical specification source | Conforms to released specs |
| Content | Specs + hygiene only | Application/infrastructure implementation |
| Gaps | Resolved here | Returned here |
| Vendors | May define capability boundaries/open decisions | May configure selected adapters after decision closure |
| Deploy | Does not redefine semantics | Must preserve released contracts |
| Undocumented behavior | Not canonical | Not canonical |

---

## 3. Epistemic discipline

Use `OBSERVED`, `INFERRED`, `SPECULATIVE`, `NOT_COMPUTABLE`, `DECISION_PENDING`, and `FUTURE` as defined in the specification set. Do not upgrade a label without evidence or an owner decision.

---

## 4. Forbidden vague claims

Do not use `AI-powered`, `smart matching`, `seamless`, `intelligent`, `automatically handles`, or equivalent language as a contract unless exact inputs, outputs, limits, failure modes, and authority are specified.

---

## 5. Repository boundary

Agents working in `SUAS-specs` do not add application/production code, provider credentials, real veteran data, or vendor-specific deployment implementation.

---

## 6. Acceptance and release are owner-only

Only `@scrimshawlife-ctrl` may change lifecycle.

- Agents must not tick [SPEC-001.md](SPEC-001.md).
- Completing SPEC-001 may support owner acceptance of SPEC-001 authority files only.
- Later roadmap artifacts remain independently draft until their own stages are accepted.
- `accepted` is not implementation authority.
- The first released implementation-authoritative specification cut occurs at **SPEC-016** under [ROADMAP.md](ROADMAP.md).
- Agents must not mark the stack or any artifact `released`.
