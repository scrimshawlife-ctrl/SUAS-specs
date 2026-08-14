# STATUS.md — SUAS specification status (v0.1.0)

**SPEC-001 status:** `READY_FOR_REVIEW` (not `accepted`; not `released`; see [SPEC-001.md](SPEC-001.md))  
**Related:** [README.md](README.md), [ROADMAP.md](ROADMAP.md), [VERSIONING.md](VERSIONING.md), [TESTING.md](TESTING.md), [DECISIONS.md](DECISIONS.md), [COMPLIANCE.md](COMPLIANCE.md), [APIS.md](APIS.md), [ONBOARDING.md](ONBOARDING.md), [SPEC-001.md](SPEC-001.md)

---

## 1. Identity

| Field | Value |
|---|---|
| Product | Shut Up and Serve |
| System | SUAS |
| Specification repository | `scrimshawlife-ctrl/SUAS-specs` |
| Implementation repository | `scrimshawlife-ctrl/SUAS` (`https://github.com/scrimshawlife-ctrl/SUAS`) |
| Specification version | `0.1.0` |
| Specification lifecycle | `draft` |
| SPEC-001 | `READY_FOR_REVIEW` (worksheet [SPEC-001.md](SPEC-001.md); not `accepted`) |
| Phase | `SPECIFICATION_BOOTSTRAP` |
| Implementation authority | `NOT_YET_RELEASED` |
| Pilot readiness | `NOT_READY` |

`SUAS-specs` is the canonical specification authority. `SUAS` is the implementation repository and must conform to released specifications. Draft specifications are not implementation authority.

---

## 2. Phase definition

`SPECIFICATION_BOOTSTRAP` means:

- The v0.1 specification stack exists as markdown.
- No specification artifact has been `accepted` or `released` (see [VERSIONING.md](VERSIONING.md)).
- The implementation repository has no released-spec contract to implement.
- The pilot must not be operated on this stack until specs are `accepted` and an implementation is tested against the MVP acceptance gate.

Next step: owner review of [SPEC-001.md](SPEC-001.md). SPEC-001 is `READY_FOR_REVIEW`. It is not `accepted`. That review is **not** implementation. Agents must not self-accept.

---

## 3. MVP acceptance gate

The MVP is not accepted until **all** of the following gates pass. Gate definitions are testable in [TESTING.md](TESTING.md). This file records status only.

| Gate | Meaning | Status |
|---|---|---|
| **AUTH** | Veteran passwordless auth works; responder/admin MFA works; sessions invalidate; revoked users cannot act. | `NOT_READY` |
| **CONSENT** | Consent Grants are first-class; revocation stops future use; no share/notify without a matching grant. | `NOT_READY` |
| **CHECK-IN** | Versioned questionnaire; incomplete/abandoned handled; Check-In is not treated as a Support Signal. | `NOT_READY` |
| **COORDINATION** | Support Case and Service Request state machines execute only documented transitions; assignment is not fulfillment. | `NOT_READY` |
| **SAFETY** | Red-state surfaces approved crisis resources, prioritizes human review, follows consent; no automated emergency dispatch; no diagnosis claim. | `NOT_READY` |
| **PRIVACY** | Minimization enforced; discouraged data not collected; access logged; no production data in non-prod. | `NOT_READY` |
| **OPERATIONS** | Coverage, queue review, resource verification, overdue follow-up, incident path exist and are staffed for the pilot. | `NOT_READY` |
| **REPORTING** | Operational metrics in [ANALYTICS.md](ANALYTICS.md) can be produced. Clinical-efficacy and suicides-prevented metrics are absent by design. | `NOT_READY` |

Overall MVP gate: `NOT_READY`.

---

## 4. Artifact version status

| Artifact | Current version | Lifecycle |
|---|---|---|
| Specification stack | `0.1.0` | `draft` |
| Questionnaire | `NOT_COMPUTABLE` (no published QuestionnaireVersion) | — |
| Support-signal rules | `DECISION_PENDING` (see [DECISIONS.md](DECISIONS.md)) | — |
| Event schema | `0.1.0` (specified, not released) | `draft` |
| API | `0.1.0` (specified, not released) | `draft` |
| Consent template | `NOT_COMPUTABLE` (no published template) | — |

---

## 5. What is established vs open

**Established in this stack (canonical decisions):**

- Product identity, mission, non-goals
- Canonical loop and non-interchangeable concepts
- Role set
- MVP vs future service categories
- Modular-monolith architecture for the pilot
- Entity list and logical schema shape
- Case and Service Request state names
- Consent as first-class grants
- Safety non-goals (no automated emergency dispatch, no diagnosis, no generative primary signal)
- AI policy
- Medi-Cal/billing boundary (`FUTURE`)
- Pilot size and county
- Epistemic labeling
- Cross-repo authority (specs canonical; implementation cites)
- Compliance is a **register**, not a claim ([COMPLIANCE.md](COMPLIANCE.md))
- External APIs are capability ports; vendors D-001–D-005 remain open ([APIS.md](APIS.md))
- First-run is a gated, auditable bootstrap ([ONBOARDING.md](ONBOARDING.md))
- SPEC-001 worksheet exists and is `READY_FOR_REVIEW` ([SPEC-001.md](SPEC-001.md)); lifecycle remains `draft`
- MVP enrollment identity (`INFERRED`; D-016 open): self-attest + working email and/or phone; no VA API, no DD-214, no in-person proofing for this pilot
- MVP veteran visibility (`INFERRED`; D-015 open): own Check-Ins, own Service Request status, Settlement fields written for them, Follow-Up prompts; not full Case Notes, not other veterans, not responder queue fields, not other orgs
- Notification attempt shape: one Notification row; retries append immutable Audit Events ([NOTIFICATIONS.md](NOTIFICATIONS.md))
- Contact log commands specified: `log-contact-attempt`, `complete-contact` ([API.md](API.md))

**Open:** see [DECISIONS.md](DECISIONS.md). Do not guess.

---

## 6. Implementation handoff

Implementation authority remains `NOT_YET_RELEASED` until:

1. This stack is reviewed.
2. Required artifacts reach `accepted` then `released` per [VERSIONING.md](VERSIONING.md).
3. [SPEC-001](ROADMAP.md) exit criteria are met.

Do not treat the existence of the [SUAS](https://github.com/scrimshawlife-ctrl/SUAS) repository as permission to ship against draft specs.
