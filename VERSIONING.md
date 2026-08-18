# VERSIONING.md — Specification and artifact versioning (SUAS v0.1)

**SPEC-001 status:** `READY_FOR_REVIEW`  
**Stack:** `0.1.0` / `draft`  
**Related:** [STATUS.md](STATUS.md), [ROADMAP.md](ROADMAP.md), [CHANGELOG.md](CHANGELOG.md), [AGENTS.md](AGENTS.md), [SPEC-016.md](SPEC-016.md)

---

## 1. Lifecycle

Each specification artifact has exactly one lifecycle:

| State | Meaning | Implementation authority? |
|---|---|---|
| `draft` | under active edit/preflight | No |
| `accepted` | owner-reviewed/frozen for its roadmap stage | No |
| `released` | included in a named implementation-authoritative release cut | Yes |
| `superseded` | replaced by later released artifact | historical only |

Preflight is not a lifecycle state. A preflight-complete draft remains `draft`.

---

## 2. Semantic versioning

The stack begins at `0.1.0`.

- MAJOR: incompatible change to a released contract.
- MINOR: backward-compatible addition; before first release, an explicitly documented breaking clarification may use 0.y MINOR according to owner governance.
- PATCH: non-semantic clarification/cross-link/typo.

After first released 1.x, breaking contract change requires MAJOR.

Git commit SHA is provenance, not the product/spec version.

---

## 3. Owner-only lifecycle transitions

Only `@scrimshawlife-ctrl` may accept or release.

Rules:

1. SPEC-001 acceptance applies only to its authority files.
2. Later stages are independently accepted after their own exit criteria.
3. `accepted` never authorizes implementation.
4. First implementation-authoritative cut occurs only through **SPEC-016**.
5. Agents/implementers never self-accept/self-release or tick owner worksheets.
6. Lifecycle changes update STATUS, CHANGELOG, affected artifact metadata, and release manifest consistently.

---

## 4. SPEC-016 release manifest

A release is a pinned contract set, not “current main.”

[SPEC-016.md](SPEC-016.md) requires a manifest containing at least:

- release version/date/owner;
- source commit SHA;
- released artifact paths/versions/lifecycles;
- pinned runtime artifact versions where applicable;
- D-001–D-025 release decision ledger reference;
- enabled/manual-only/information-only/unavailable/future feature manifest;
- safe deferrals;
- supersedes relationship.

Only artifacts explicitly listed as `released` in that manifest are implementation authority.

---

## 5. Versioned runtime artifacts

Released stack pins enabled runtime artifacts such as:

| Artifact | Identifier / authority |
|---|---|
| Questionnaire | `questionnaire_version` / CHECKINS |
| Support Signal rules | `signal_version` / SUPPORT_SIGNALS |
| Event schema | `schema_version` / EVENT_MODEL |
| Product API | path/contract version (`/api/v0` if released unchanged) / API |
| Consent template | `consent_template_version` / CONSENT |
| Notification template | `template_version` / NOTIFICATIONS |
| Approved safety copy | accepted artifact/version/reference required for enabled production red-state UI |

Published/versioned historical runtime records retain the version used when created and are never silently rewritten.

Provider vendors, infrastructure products, capacity targets, SLOs, recovery objectives, and reporting privacy thresholds are decision/configuration records unless a released contract explicitly versions them; they do not redefine canonical domain behavior.

---

## 6. Implementation citations

Implementation PRs cite:

1. released spec file/section;
2. released stack/artifact version;
3. lifecycle = `released`;
4. relevant runtime artifact pins;
5. applicable readiness/conformance contract;
6. release manifest/feature-availability boundary when relevant.

A citation to draft/accepted/preflight work does not make it authoritative.

---

## 7. Historical replay

Check-Ins retain questionnaire version; Signals retain computation/rule/input identity; Consent retains template version; Notifications retain template/logical-send identity; Domain/Audit Events retain schema identity; Settlement cycles and FulfillmentAttempts preserve historical business meaning.

Recomputation/reopen/reroute creates new linked history rather than silently mutating old meaning.

---

## 8. Release vs readiness

`released` means implementation-authoritative. It does **not** mean production-ready.

After SPEC-016:

- SPEC-017 verifies implementation conformance;
- SPEC-018 verifies launch readiness from test/load/failure/restore/operations evidence.

---

## 9. Non-goals

- blanket acceptance from one stage;
- releasing all files merely because they are in one PR/branch;
- using git SHA as spec version;
- selecting providers/infrastructure in this file;
- treating release as launch approval;
- releasing `UNRELEASED_FIXTURE` artifacts or unreviewed implementation defaults.
