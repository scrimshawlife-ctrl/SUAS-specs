# AGENTS.md — Required rules for agents and implementers

**SPEC-001 status:** `READY_FOR_REVIEW` (not `accepted`; not `released`; see [SPEC-001.md](SPEC-001.md))  
**Related:** [README.md](README.md), [CONTRIBUTING.md](CONTRIBUTING.md), [VERSIONING.md](VERSIONING.md), [STATUS.md](STATUS.md), [SPEC-001.md](SPEC-001.md)

This file binds human and automated agents working on SUAS specifications or the SUAS implementation.

---

## 1. Ten required agent rules

1. **Specs are authority.** `SUAS-specs` is the canonical specification authority. `SUAS` (`https://github.com/scrimshawlife-ctrl/SUAS`) is the implementation repository and must conform to **released** specs.
2. **Cite contracts.** Every implementation PR in `SUAS` must cite spec file, section, stack version, lifecycle, and relevant artifact versions (questionnaire, signal, event schema, API, consent template).
3. **Gaps return to specs.** If implementation cannot proceed without an unstated rule, stop and open specification work. Do not invent the rule in code.
4. **Deploy does not redefine spec.** Environment topology, vendor choice, and runtime flags must not change domain semantics.
5. **Undocumented implementation is not canonical.** Behavior that exists only in code is not a product rule until specified and released.
6. **Do not guess open decisions.** Follow [DECISIONS.md](DECISIONS.md). Use `DECISION_PENDING` / `NOT_COMPUTABLE`.
7. **Do not invent external facts.** No invented partner capabilities, county agreements, VA integrations, Medi-Cal eligibility, responder coverage, clinical claims, reimbursement, or legal status.
8. **No safety-critical generative AI.** Do not use a generative model for the primary Support Signal, suicidality determination, emergency intervention, trusted-contact notify, service qualification, or case closure. See [SAFETY.md](SAFETY.md) and [ARCHITECTURE.md](ARCHITECTURE.md).
9. **No automated emergency dispatch.** Do not implement 911/auto-dispatch. Do not imply SUAS replaces emergency services. See [SAFETY.md](SAFETY.md).
10. **Match names exactly.** State-machine names, event types, role names, and glossary terms must match this stack. Do not alias `Support Case` to `ticket` or `Service Request` to `referral` in contracts.

---

## 2. Cross-repo governance

| Rule | Specs repo (`SUAS-specs`) | Implementation repo (`SUAS`) |
|---|---|---|
| Authority | Canonical | Must conform to released specs |
| Code | Markdown + hygiene only | Application code that cites specs |
| Gaps | Resolved here | Returned here; not papered over |
| Deploy | Does not select a cloud vendor | Must not redefine domain semantics |
| Undocumented behavior | Not present | Not canonical |

Implementation PRs that lack spec citations are incomplete.

---

## 3. Epistemic discipline

Use `OBSERVED`, `INFERRED`, `SPECULATIVE`, `NOT_COMPUTABLE`, `DECISION_PENDING`, `FUTURE` as defined in [README.md](README.md). Do not upgrade a label without new evidence.

---

## 4. Forbidden language unless behavior is fully defined

Do not write "AI-powered", "smart matching", "seamless", "intelligent", or "automatically handles" unless the exact behavior, inputs, outputs, and failure modes are specified in a released spec.

---

## 5. This repository

Agents working in `SUAS-specs` must not clone implementation repos, must not write application/production code, and must not claim a spec is `accepted` or `released`.

## 6. Acceptance is owner-only

Only the specification owner (`@scrimshawlife-ctrl`) may set lifecycle to `accepted` in [STATUS.md](STATUS.md), [VERSIONING.md](VERSIONING.md), and [CHANGELOG.md](CHANGELOG.md) after the [SPEC-001.md](SPEC-001.md) checklist is complete. Agents must not self-accept. Agents must not tick the SPEC-001 checklist. `accepted` is not `released`. See [VERSIONING.md](VERSIONING.md) section 3.1.
