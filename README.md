# Shut Up and Serve (SUAS) — Specification v0.1

**Product:** Shut Up and Serve  
**System identifier:** SUAS  
**Specification version:** 0.1.0  
**Specification status:** `draft` (see [VERSIONING.md](VERSIONING.md))  
**SPEC-001 status:** `READY_FOR_REVIEW` (not `accepted`; not `released`; see [SPEC-001.md](SPEC-001.md))  
**Phase:** `SPECIFICATION_BOOTSTRAP` (see [STATUS.md](STATUS.md))

This repository (`SUAS-specs`) is the **canonical specification authority** for Shut Up and Serve. The implementation repository ([SUAS](https://github.com/scrimshawlife-ctrl/SUAS)) must conform to released specifications. Undocumented implementation is not canonical. Gaps discovered during implementation return to this repository as specification work, not as silent product redefinition.

---

## 1. What SUAS is

SUAS is a **consent-governed veteran support coordination platform**.

It coordinates the shortest safe and consented path between a veteran's current need and an available human or material support resource.

SUAS is an operational coordination system. It records needs, consent, assignments, fulfillments, follow-ups, and settlements. It does not diagnose, predict crisis, or dispatch emergency services.

**Mission (canonical):** Coordinate the shortest safe and consented path between a veteran's current need and an available human or material support resource.

See [PRODUCT.md](PRODUCT.md).

---

## 2. Who it serves

| Role | Definition |
|---|---|
| **Veteran** | The person whose need is being coordinated. The primary data subject and consent granter. |
| **Responder** | A human coordinator who claims or is assigned a Support Case and works the coordination loop. |
| **Organization Administrator** | An administrator scoped to one Organization. Manages membership, responders, and org-owned resources. Not a global SUAS administrator. |
| **Trusted Contact** | A person in the veteran's Trusted Circle, with explicit, purpose-scoped Consent Grants. Membership alone grants no visibility. |
| **Service Provider** | An organization or person that can fulfill a Service Request (food, transportation, shelter, peer support). |
| **SUAS System Administrator** | A global operator of the SUAS system. Distinct from Organization Administrator. |

Role definitions are authoritative in [PRODUCT.md](PRODUCT.md) and [GLOSSARY.md](GLOSSARY.md).

---

## 3. Problem

Veterans in a local pilot (Santa Clara County, California) need timely, consented coordination of basic support: food, transportation, temporary shelter, and peer/human support. Existing systems are fragmented. Informal coordination loses consent boundaries, auditability, and follow-through.

SUAS exists to make that coordination **explicit, consented, inspectable, and completable**.

---

## 4. Operational boundaries

SUAS is **not**:

- a crisis-prediction application
- an electronic health record (EHR)
- a medical diagnosis tool
- an automated emergency-dispatch platform
- a replacement for emergency services
- a clinical efficacy measurement system
- a Medi-Cal billing system (billing adapter is `FUTURE`; see [SETTLEMENT.md](SETTLEMENT.md) and [PRODUCT.md](PRODUCT.md))

SUAS **does**:

- collect versioned Check-Ins
- compute deterministic Support Signals (`GREEN` / `YELLOW` / `ORANGE` / `RED`)
- require explicit Consent Grants before sharing or notifying
- open Support Cases and Service Requests
- coordinate Responders, Resources, and Referrals
- record Fulfillment, Follow-Up, and Settlement
- emit immutable Domain Events and Audit Events

---

## 5. Pilot scope

- **Population:** approximately 25–50 veterans
- **Geography:** Santa Clara County, California
- **MVP service categories:** `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`
- **Operational focus:** food, transportation, temporary shelter, peer/human support, responder coordination, resource referrals, trusted-circle communication, follow-up

Partner organizations are **not invented**. Use `PARTNER_ORG_001` placeholders until named. See [PILOT.md](PILOT.md) and [DECISIONS.md](DECISIONS.md).

Pilot readiness: `NOT_READY`. Implementation authority: `NOT_YET_RELEASED`.

---

## 6. Canonical loop

Every coordinated need travels this loop. Stages are sequential in meaning. A Support Case may contain multiple Service Requests; each request travels the loop independently after NEED.

```
SIGNAL → NEED → CONSENT → COORDINATION → FULFILLMENT → FOLLOW-UP → SETTLEMENT
```

| Stage | Meaning | Primary artifacts |
|---|---|---|
| **SIGNAL** | A deterministic Support Signal is computed from a Check-In (or an explicit veteran-initiated need). The signal is a coordination priority label, not a diagnosis. | [Check-In](CHECKINS.md), [Support Signal](SUPPORT_SIGNALS.md) |
| **NEED** | A concrete need is identified and recorded as one or more Service Requests on a Support Case. | [Support Case](CASES.md), [Service Request](DISPATCH.md) |
| **CONSENT** | Explicit, purpose-scoped Consent Grants are checked or obtained before any share, notify, assign-outside-org, or trusted-contact alert. | [Consent](CONSENT.md), [Trusted Circle](TRUSTED_CIRCLE.md) |
| **COORDINATION** | A Responder claims or is assigned the case; matching, assignment, referral, and contact occur. | [Cases](CASES.md), [Dispatch](DISPATCH.md), [Responder Workflows](RESPONDER_WORKFLOWS.md), [Referrals](REFERRALS.md), [Resources](RESOURCES.md) |
| **FULFILLMENT** | The requested support is accepted, started, completed, and confirmed. Assignment is not fulfillment. | [Fulfillment](FULFILLMENT.md) |
| **FOLLOW-UP** | First-class Follow-Up records with due dates, owners, retries, and escalation. Not hidden in notes. | [Follow-Up](FOLLOWUP.md) |
| **SETTLEMENT** | An explicit resolution record of what was requested, what occurred, what remains, and who confirmed. Not a clinical outcome. | [Settlement](SETTLEMENT.md) |

These concepts are **not interchangeable**: Check-In, Support Signal, Support Case, Service Request, Referral, Assignment, Fulfillment, Follow-Up, Settlement. See [GLOSSARY.md](GLOSSARY.md).

**Support Case** = coordination around a veteran.  
**Service Request** = a specific requested need.  
One case may contain multiple service requests.

---

## 7. Specification / implementation relationship

| Repository | Role |
|---|---|
| **SUAS-specs** (`scrimshawlife-ctrl/SUAS-specs`) | Canonical specification authority. Released specs define contracts. |
| **SUAS** (`https://github.com/scrimshawlife-ctrl/SUAS`) | Implementation repository. Must conform to released specs. Must cite spec sections and versions in PRs. |

Rules:

1. Specifications are the authority.
2. Implementation cites contracts; it does not redefine them.
3. Gaps return to specifications.
4. Deployment does not redefine specification.
5. Undocumented implementation is not canonical.

See [AGENTS.md](AGENTS.md), [VERSIONING.md](VERSIONING.md), [CONTRIBUTING.md](CONTRIBUTING.md).

---

## 8. Document index

### Product and authority

| File | Purpose |
|---|---|
| [PRODUCT.md](PRODUCT.md) | Mission, roles, categories, loop, non-goals, Medi-Cal/billing boundary |
| [GLOSSARY.md](GLOSSARY.md) | Canonical terms |
| [STATUS.md](STATUS.md) | Phase, readiness, MVP acceptance gate |
| [VERSIONING.md](VERSIONING.md) | Semver, artifact versions, lifecycle |
| [ROADMAP.md](ROADMAP.md) | SPEC-001 through SPEC-015 |
| [DECISIONS.md](DECISIONS.md) | Open decisions; do not guess |
| [AGENTS.md](AGENTS.md) | Agent rules and cross-repo governance |

### Architecture and data

| File | Purpose |
|---|---|
| [ARCHITECTURE.md](ARCHITECTURE.md) | Modular monolith, modules, jobs, AI policy |
| [DOMAIN_MODEL.md](DOMAIN_MODEL.md) | Entities, ownership, lifecycle, authz |
| [DATA_MODEL.md](DATA_MODEL.md) | Logical schema |
| [EVENT_MODEL.md](EVENT_MODEL.md) | Domain and audit events |
| [API.md](API.md) | Resource/domain contract |
| [APIS.md](APIS.md) | Necessary-API inventory (Plane A minimum + Plane B capability ports). API.md is the contract; APIS.md is the inventory. |

### Domain specs

| File | Purpose |
|---|---|
| [AUTH.md](AUTH.md) | Authentication, sessions, MFA, recovery |
| [CONSENT.md](CONSENT.md) | Consent grants, revocation, purpose scope |
| [CHECKINS.md](CHECKINS.md) | Versioned questionnaires |
| [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md) | Deterministic GREEN/YELLOW/ORANGE/RED |
| [SAFETY.md](SAFETY.md) | Red-state behavior, AI policy, crisis-resource surfacing |
| [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md) | Invites, permissions, consent dependencies |
| [CASES.md](CASES.md) | Support Case state machine |
| [DISPATCH.md](DISPATCH.md) | Service Request state machine |
| [RESOURCES.md](RESOURCES.md) | Resource catalog and freshness |
| [REFERRALS.md](REFERRALS.md) | Referrals distinct from Service Requests |
| [FULFILLMENT.md](FULFILLMENT.md) | Acceptance through confirmation |
| [FOLLOWUP.md](FOLLOWUP.md) | First-class follow-up |
| [SETTLEMENT.md](SETTLEMENT.md) | Resolution record and funding boundary |
| [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md) | Responder actions and queue |
| [NOTIFICATIONS.md](NOTIFICATIONS.md) | Channels, consent basis, templates |

### Security, privacy, operations

| File | Purpose |
|---|---|
| [SECURITY.md](SECURITY.md) | Controls, threats, HIPAA_APPLICABILITY = DECISION_PENDING |
| [PRIVACY.md](PRIVACY.md) | Minimization, collection boundaries, retention |
| [COMPLIANCE.md](COMPLIANCE.md) | Compliance register (not a claim of being compliant) |
| [ONBOARDING.md](ONBOARDING.md) | Admin first-run bootstrap and first-time user experience |
| [ADMIN.md](ADMIN.md) | Administration surfaces |
| [PILOT.md](PILOT.md) | Pilot operations |
| [ANALYTICS.md](ANALYTICS.md) | Operational metrics only |
| [TESTING.md](TESTING.md) | Test suites and MVP acceptance gate |
| [DEPLOYMENT.md](DEPLOYMENT.md) | Environments; cloud = DECISION_PENDING |
| [OPERATIONS.md](OPERATIONS.md) | Day-2 ops |
| [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md) | Technical and safety incidents |

### Process

| File | Purpose |
|---|---|
| [CONTRIBUTING.md](CONTRIBUTING.md) | How to change specs |
| [SPEC-001.md](SPEC-001.md) | SPEC-001 owner-review worksheet (`READY_FOR_REVIEW`, not `accepted`) |
| [CHANGELOG.md](CHANGELOG.md) | 0.1.0 bootstrap |
| [SPEC_AUDIT.md](SPEC_AUDIT.md) | Completeness audit of this stack |
| [CODEOWNERS](CODEOWNERS) | `* @scrimshawlife-ctrl` |

---

## 9. Current status

| Field | Value |
|---|---|
| Product | Shut Up and Serve |
| System | SUAS |
| Specs repo | `scrimshawlife-ctrl/SUAS-specs` |
| Implementation repo | `scrimshawlife-ctrl/SUAS` |
| Phase | `SPECIFICATION_BOOTSTRAP` |
| Implementation authority | `NOT_YET_RELEASED` |
| Pilot readiness | `NOT_READY` |
| Spec version | `0.1.0` (`draft`) |
| SPEC-001 | `READY_FOR_REVIEW` (not `accepted`) |

See [STATUS.md](STATUS.md).

---

## 10. How engineers use this repository

1. Read [PRODUCT.md](PRODUCT.md), [GLOSSARY.md](GLOSSARY.md), and this README before any implementation work.
2. Treat released specifications as contracts. Draft specifications are not implementation authority.
3. Cite spec file, section, and version in every implementation PR in SUAS. See [AGENTS.md](AGENTS.md).
4. Do not invent partner capabilities, county agreements, VA integrations, Medi-Cal eligibility, responder coverage, clinical claims, reimbursement, or legal status. Label unknowns with `OBSERVED` / `INFERRED` / `SPECULATIVE` / `NOT_COMPUTABLE` or `DECISION_PENDING`.
5. Do not implement generative AI for safety-critical decisions. See [SAFETY.md](SAFETY.md) and [ARCHITECTURE.md](ARCHITECTURE.md).
6. If implementation discovers a gap, open specification work here. Do not paper over the gap in code.
7. State-machine names in code must match [CASES.md](CASES.md) and [DISPATCH.md](DISPATCH.md) exactly.
8. Run the critical test suites listed in [TESTING.md](TESTING.md) before claiming a domain is implemented.
9. Do not claim HIPAA compliance. `HIPAA_APPLICABILITY = DECISION_PENDING`.
10. Do not write production application code in this repository. This repository is specifications and hygiene files only.

---

## 11. Epistemic labels

Use these labels whenever a claim is not a released product rule:

| Label | Meaning |
|---|---|
| `OBSERVED` | Directly evidenced in this specification set or an approved source. |
| `INFERRED` | Derived from stated rules; not independently evidenced. |
| `SPECULATIVE` | Possible; not decided; must not be implemented as fact. |
| `NOT_COMPUTABLE` | Cannot be determined from available information. Do not invent a value. |
| `DECISION_PENDING` | An open product/engineering/legal decision. See [DECISIONS.md](DECISIONS.md). |
| `FUTURE` | Explicitly out of MVP scope. Specified only as a boundary. |

---

## 12. Quality rules (this stack)

- Explicit, non-redundant, internally cross-linked with relative markdown links.
- Implementation-oriented and testable.
- Clear assumptions, non-goals, and authority.
- No marketing language ("AI-powered", "smart matching", "seamless", "intelligent", "automatically handles") unless exact behavior is defined.
- No unsupported HIPAA/compliance claims.
- No unsupported clinical claims.
- No automated emergency dispatch.
- State-machine names match across files.
- Terminology matches [GLOSSARY.md](GLOSSARY.md).
- SUAS-specs is consistently canonical; SUAS is consistently implementation.
