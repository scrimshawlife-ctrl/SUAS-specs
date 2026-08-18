# VERSIONING.md — Specification and artifact versioning (SUAS v0.1)

**SPEC-001 status:** `READY_FOR_REVIEW` (not `accepted`; not `released`)  
**Related:** [STATUS.md](STATUS.md), [ROADMAP.md](ROADMAP.md), [CHANGELOG.md](CHANGELOG.md), [CONTRIBUTING.md](CONTRIBUTING.md), [AGENTS.md](AGENTS.md), [SPEC-001.md](SPEC-001.md)

---

## 1. Purpose

Define how specification and runtime-artifact versions change, how implementations cite them, and when a version becomes implementation authority.

---

## 2. Semantic versioning

The specification stack begins at `0.1.0`.

| Component | Meaning |
|---|---|
| MAJOR | Incompatible change to a released contract |
| MINOR | Backward-compatible contract addition; while unreleased 0.y, may also carry explicitly documented breaking clarification |
| PATCH | Non-semantic clarification, typo, cross-link, or equivalent correction |

After the first released 1.x contract, breaking contract changes require MAJOR.

---

## 3. Lifecycle

Every versioned specification artifact has one lifecycle state:

| State | Meaning | Implementation authority? |
|---|---|---|
| `draft` | Under active edit | No |
| `accepted` | Owner-reviewed/frozen for its roadmap stage | No |
| `released` | Canonical implementation contract | Yes |
| `superseded` | Replaced by a later released version | Historical only |

Current stack: `0.1.0` / `draft`.

A file may describe `FUTURE` behavior without that behavior being released.

### 3.1 Owner-only lifecycle transitions

Only `@scrimshawlife-ctrl` may change lifecycle state.

Agents and implementers must never self-accept or self-release.

Lifecycle promotion is **artifact/stage specific**:

1. Completing [SPEC-001.md](SPEC-001.md) can support owner acceptance of the SPEC-001 authority files only.
2. It does not accept later roadmap artifacts.
3. Later artifacts are accepted only when their own [ROADMAP.md](ROADMAP.md) stage exit criteria are met.
4. `accepted` does not authorize implementation.
5. The first implementation-authoritative released cut occurs only at **SPEC-016** after its dependencies and safe decision/defer rules are satisfied.
6. Lifecycle changes must be reflected consistently in [STATUS.md](STATUS.md), [CHANGELOG.md](CHANGELOG.md), and the affected artifact/version metadata.

---

## 4. Versioned runtime artifacts

Released stack versions pin the runtime artifact versions they include.

| Artifact | Identifier | Authority |
|---|---|---|
| Questionnaire | `questionnaire_version` | [CHECKINS.md](CHECKINS.md) |
| Support Signal rules | `signal_version` | [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md) |
| Event schema | `schema_version` | [EVENT_MODEL.md](EVENT_MODEL.md) |
| Product API | contract/path version | [API.md](API.md) |
| Consent template | `consent_template_version` | [CONSENT.md](CONSENT.md) |
| Notification template | `template_version` | [NOTIFICATIONS.md](NOTIFICATIONS.md) |

Published/versioned runtime objects are not silently mutated. Historical records retain the versions used when created.

Provider adapters, infrastructure vendors, capacity targets, SLOs, and recovery targets are deployment/decision configuration unless a released contract explicitly versions them. Their selection must not redefine canonical domain behavior.

---

## 5. Implementation citation requirements

Implementation PRs in `SUAS` must cite:

1. spec file;
2. section;
3. stack version;
4. lifecycle;
5. relevant runtime artifact versions;
6. applicable readiness/conformance contract when touching UI, provider integration, scaling, resilience, security, or operations.

A citation to a `draft` or `accepted` artifact does not turn it into implementation authority.

---

## 6. Historical replay

- Check-Ins retain `questionnaire_version`.
- Support Signals retain rule/input versions, timestamp, and basis.
- Consent Grants retain template version.
- Domain/Audit Events retain schema version.
- Recalculation writes a new record rather than overwriting historical meaning.

---

## 7. Non-goals

- choosing a provider or infrastructure vendor;
- inventing a marketing version name;
- using git commit hashes as product specification versions;
- treating owner acceptance of one roadmap stage as blanket acceptance or release of the entire stack.
