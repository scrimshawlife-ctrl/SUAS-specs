# CONTRIBUTING.md — How to change SUAS specifications

**Related:** [AGENTS.md](AGENTS.md), [VERSIONING.md](VERSIONING.md), [CHANGELOG.md](CHANGELOG.md), [README.md](README.md)

This repository is specifications and hygiene files only. Do not add application or production code.

---

## 1. Who may change specs

- Specification owner: `@scrimshawlife-ctrl` (see [CODEOWNERS](CODEOWNERS)).
- Implementation contributors propose spec changes here when they find gaps. They do not redefine contracts in `SUAS`.

---

## 2. Allowed change types

| Type | Examples | Version impact |
|---|---|---|
| Clarification | Cross-links, wording that does not change behavior | PATCH |
| Additive contract | New optional field, new `FUTURE` section | MINOR |
| Breaking contract | State rename, required field, consent semantic change | MAJOR (or explicit 0.y breaking MINOR if still unreleased) |
| Decision close | Moving an item out of [DECISIONS.md](DECISIONS.md) | MINOR or MAJOR depending on impact |

---

## 3. Process

1. Read [GLOSSARY.md](GLOSSARY.md) and [PRODUCT.md](PRODUCT.md). Use those terms.
2. Edit the minimum set of files. Keep state-machine names identical across files.
3. Update cross-links.
4. Update [CHANGELOG.md](CHANGELOG.md) and [STATUS.md](STATUS.md) if phase or gate status changes.
5. If you close or open a decision, update [DECISIONS.md](DECISIONS.md).
6. Do not invent partners, county agreements, VA integrations, Medi-Cal eligibility, responder coverage, clinical claims, reimbursement, or legal status.
7. Label unknowns with `OBSERVED` / `INFERRED` / `SPECULATIVE` / `NOT_COMPUTABLE` / `DECISION_PENDING` / `FUTURE`.
8. Do not claim HIPAA compliance.
9. Implementation PRs belong in `https://github.com/scrimshawlife-ctrl/SUAS` and must cite spec sections and versions.

---

## 4. What not to contribute here

- Application code, SDKs, infrastructure-as-code that selects a cloud vendor, seed data with real veteran information, marketing copy, clinical protocols presented as product claims.

---

## 5. Review bar

A spec change is acceptable when:

- Terminology matches [GLOSSARY.md](GLOSSARY.md).
- State names match [CASES.md](CASES.md) and [DISPATCH.md](DISPATCH.md).
- Authority remains: specs canonical; implementation cites.
- Testability is stated for new rules.
- Non-goals remain intact (no automated emergency dispatch, no diagnosis, no generative primary signal).
