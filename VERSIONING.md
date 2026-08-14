# VERSIONING.md — Specification and artifact versioning (SUAS v0.1)

**Related:** [STATUS.md](STATUS.md), [ROADMAP.md](ROADMAP.md), [CHANGELOG.md](CHANGELOG.md), [CONTRIBUTING.md](CONTRIBUTING.md), [AGENTS.md](AGENTS.md)

---

## 1. Purpose

Define how specifications and versioned runtime artifacts change, how implementations cite them, and when a version is authority.

---

## 2. Semver

The specification stack uses semantic versioning, starting at **0.1.0**.

| Component | Meaning for specs |
|---|---|
| MAJOR | Incompatible change to a released contract (state names, event envelope, consent model, API resource semantics). |
| MINOR | Backward-compatible addition (new optional field, new event type, new future category). |
| PATCH | Clarification, typo, cross-link, or non-semantic correction that does not change implementable behavior. |

While the stack is `0.y.z` (`draft` or `accepted` but not yet relied on by a released implementation), MINOR may include breaking clarifications if [CHANGELOG.md](CHANGELOG.md) says so explicitly. After the first `released` 1.x contract, breaking changes require MAJOR.

---

## 3. Lifecycle states

Every versioned specification artifact has exactly one lifecycle state:

| State | Meaning | Implementation may treat as authority? |
|---|---|---|
| `draft` | Under edit. May change without notice. | No |
| `accepted` | Reviewed and frozen pending release. | No (cite only as upcoming) |
| `released` | Canonical contract. Implementation must conform. | Yes |
| `superseded` | Replaced by a later released version. Kept for audit. | No (except for historical replay) |

Current stack: `0.1.0` / `draft`. See [STATUS.md](STATUS.md).

A file in this repository may describe future behavior (`FUTURE`) without that behavior being `released`.

---

## 4. Versioned artifacts

These artifacts are versioned independently of the stack version, but a released stack version must pin the artifact versions it includes.

| Artifact | Identifier field | Where specified | Change rule |
|---|---|---|---|
| Questionnaire | `questionnaire_version` | [CHECKINS.md](CHECKINS.md) | Published versions are immutable. New content = new version. |
| Support-signal rules | `signal_version` | [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md) | Published rule sets are immutable. Historical calculations are never silently mutated. |
| Event schema | `schema_version` on the event envelope | [EVENT_MODEL.md](EVENT_MODEL.md) | Additive MINOR; breaking envelope changes are MAJOR. |
| API | API version prefix / contract version | [API.md](API.md) | Resource/domain contract. Breaking path or semantics = MAJOR. |
| Consent template | `consent_template_version` | [CONSENT.md](CONSENT.md) | Grants reference the template version they were accepted under. |

Additional versioned runtime objects:

- Notification templates (`template_version`) — [NOTIFICATIONS.md](NOTIFICATIONS.md)
- Resource verification is timestamped (`last_verified_at`), not versioned as a spec artifact.

---

## 5. Citation requirements

Implementation PRs in `SUAS` must cite:

1. Spec file (for example `CASES.md`)
2. Section
3. Spec stack version (for example `0.1.0`)
4. Lifecycle (`draft` / `accepted` / `released`)
5. Artifact versions if the change touches questionnaire, signal rules, events, API, or consent templates

See [AGENTS.md](AGENTS.md).

---

## 6. Replay and historical records

- Historical Check-Ins keep their `questionnaire_version`.
- Historical Support Signals keep `signal_version`, `input_questionnaire_version`, `computed_at`, and basis.
- Historical Consent Grants keep `consent_template_version`.
- Historical Domain Events and Audit Events keep `schema_version`.
- Recalculation of a historical Support Signal, if ever performed, writes a **new** record; it does not overwrite the original.

---

## 7. Non-goals

- This file does not version cloud providers, vendor SDKs, or deployment topology (`DECISION_PENDING`).
- This file does not invent a marketing version name.
- Git commit hashes are not specification versions.
