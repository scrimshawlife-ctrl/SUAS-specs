# TESTING.md — Test suites and MVP acceptance gate (SUAS v0.1)

**Related:** [STATUS.md](STATUS.md), [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [CONSENT.md](CONSENT.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [SAFETY.md](SAFETY.md), [SECURITY.md](SECURITY.md)

---

## 1. Purpose

Define the test layers and the **critical suites** that must pass before a domain is claimed implemented. Define the MVP acceptance gate (also recorded in [STATUS.md](STATUS.md)).

Draft specs are not implementation authority. Tests in `SUAS` must cite this file and the domain spec they encode.

---

## 2. Layers

| Layer | What it proves |
|---|---|
| Unit | Pure functions (signal compute, validators, freshness band) |
| Domain / state-machine | Only documented transitions; fixtures for every edge in [CASES.md](CASES.md) and [DISPATCH.md](DISPATCH.md) |
| Authorization | Role × action matrix |
| Tenant-isolation | Org A cannot read Org B (404/403, empty lists) |
| Integration | Module boundaries + PostgreSQL |
| API | [API.md](API.md) contract |
| Notification | Enqueue/send/retry/revoke-before-send |
| Security | Threat categories in [SECURITY.md](SECURITY.md) |
| Migration | Schema up/down on empty and fixture-filled DB |
| End-to-end | Veteran PWA + responder console against TEST |
| Pilot simulation | N<=50 synthetic veterans, full loop, metrics, no prod data |

---

## 3. Critical suites (required)

| Suite | Spec | Must prove |
|---|---|---|
| Support-signal determinism | [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md) | Same inputs + versions → same level + basis; no historical mutation |
| Consent revocation | [CONSENT.md](CONSENT.md) | Revoke stops future view/notify/refer; history preserved |
| Trusted-circle visibility | [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md) | Membership without grants sees nothing listed |
| Cross-tenant isolation | [SECURITY.md](SECURITY.md) | No leakage via API, jobs, or search |
| Service-request transitions | [DISPATCH.md](DISPATCH.md) | Illegal edges fail; assign ≠ fulfill |
| Case transitions | [CASES.md](CASES.md) | Illegal edges fail; close retains history; resolve needs Settlement |
| Notification consent | [NOTIFICATIONS.md](NOTIFICATIONS.md) | Grant re-checked before send |
| Red-state behavior | [SAFETY.md](SAFETY.md) | Resources surfaced; human review prioritized; no emergency auto-dispatch; consent for contacts |
| Stale-resource handling | [RESOURCES.md](RESOURCES.md) | Warning bands; inactive not assignable |
| Responder authorization | [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md) | Actions fail without assignment/role |
| Contact log | [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [API.md](API.md) | `log-contact-attempt` / `complete-contact` require `at`, `channel`, `outcome`, `actor_id` and emit `RESPONDER_CONTACT_LOGGED`; Case Note create does not |
| Veteran visibility | [CASES.md](CASES.md) section 8 | Veteran cannot read Case Notes, Contact Attempts, other veterans, queue fields, or other orgs; can read own Check-Ins, own Service Request status, Settlement fields written for them, Follow-Up prompts |
| Notification attempts | [NOTIFICATIONS.md](NOTIFICATIONS.md) | One Notification row; retries append immutable Audit Events; no child attempt table |
| Audit-event immutability | [EVENT_MODEL.md](EVENT_MODEL.md) | No update/delete via application roles |

A PR that touches a domain without updating or running the matching critical suite is incomplete.

---

## 4. MVP acceptance gate

The MVP is not accepted until **all** gates pass. Status is mirrored in [STATUS.md](STATUS.md). Current value of each: `NOT_READY`.

| Gate | Pass condition |
|---|---|
| **AUTH** | Veteran magic-link and email OTP succeed in TEST/STAGING. Responder/admin MFA required. Sessions invalidate. Revoked users cannot act. Rate limits hold. |
| **CONSENT** | Grants are first-class. Evaluation at use time. Revocation stops future use. Critical suite green. |
| **CHECK-IN** | Versioned questionnaire published in TEST. Incomplete/abandoned handled. Check-In is not treated as a Support Signal. |
| **COORDINATION** | Case and Service Request machines execute only documented transitions. Assignment is not fulfillment. Responder actions named in [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md) work. |
| **SAFETY** | Red-state suite green. No emergency auto-dispatch. No diagnosis claim in UI. AI policy respected. |
| **PRIVACY** | Minimization enforced (no SSN/service-record/medical-history/DD-214 fields). Access logged. No prod data in non-prod. Sensitive values absent from logs. Enrollment does not require a VA identity API or in-person proofing (D-016 MVP default). |
| **OPERATIONS** | Coverage, queue review, resource verification, overdue follow-up, incident path exist and are staffed per [OPERATIONS.md](OPERATIONS.md) / [PILOT.md](PILOT.md) (D-009 may still constrain hours but the path exists). |
| **REPORTING** | Allowed metrics in [ANALYTICS.md](ANALYTICS.md) can be produced. Forbidden clinical metrics are absent. |

Overall: `NOT_READY` until SPEC-014.

---

## 5. Fixtures

- Use synthetic veterans only.
- Golden vectors for signals wait on D-011; until then, the engine interface is tested with placeholder versions that are clearly `UNRELEASED_FIXTURE` and must not ship as production `signal_version`.
- Safety copy tests use a `TEST_SAFETY_COPY` slot until D-012.

---

## 6. Non-goals

- Testing unnamed vendor SLAs
- Using production veteran data
- Claiming clinical validation

---

## 7. Handoff

After SPEC-008 acceptance, `SUAS` implements this plan and cites suites in PRs ([AGENTS.md](AGENTS.md)).
