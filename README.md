# Shut Up and Serve (SUAS) — Specification v0.1

**Product:** Shut Up and Serve  
**System identifier:** SUAS  
**Specification version:** 0.1.0  
**Specification status:** `draft`  
**SPEC-001 status:** `READY_FOR_REVIEW` (not accepted/released)  
**Phase:** `SPECIFICATION_BOOTSTRAP`

This repository (`SUAS-specs`) is the **canonical specification authority** for Shut Up and Serve. The implementation repository (`scrimshawlife-ctrl/SUAS`) must conform to released specifications. Undocumented implementation is not canonical.

---

## 1. Mission

SUAS is a **consent-governed veteran support coordination platform**.

**Canonical mission:** Coordinate the shortest safe and consented path between a veteran's current need and an available human or material support resource.

SUAS records needs, consent, assignments, fulfillment, follow-up, and settlement. It does not diagnose, predict crisis, or automate emergency dispatch.

See [PRODUCT.md](PRODUCT.md).

---

## 2. Roles

| Role | Definition |
|---|---|
| Veteran | Primary data subject and consent granter whose need is coordinated |
| Responder | Human coordinator who claims/is assigned a Support Case |
| Organization Administrator | Admin scoped to one Organization |
| Trusted Contact | Invited person with only explicitly consented visibility |
| Service Provider | Person/organization capable of fulfilling a Service Request; API not required |
| SUAS System Administrator | Global system operator, distinct from Organization Administrator |

---

## 3. Canonical loop

```text
SIGNAL → NEED → CONSENT → COORDINATION → FULFILLMENT → FOLLOW-UP → SETTLEMENT
```

These concepts are not interchangeable: Check-In, Support Signal, Support Case, Service Request, Referral, Assignment, Fulfillment, Follow-Up, Settlement.

**Support Case** = coordination around a veteran.  
**Service Request** = one specific requested need.  
One Case may contain multiple Service Requests.

---

## 4. MVP scope

- Controlled pilot: approximately 25–50 veterans
- Geography: Santa Clara County, California
- MVP service categories: `FOOD`, `TRANSPORTATION`, `SHELTER`, `PEER_SUPPORT`
- Partner organizations remain placeholders until decided

Pilot readiness: `NOT_READY`.  
Production readiness: `NOT_READY`.  
Implementation authority: `NOT_YET_RELEASED`.

**The pilot size is an operating scope, not an architecture ceiling.** Production design must avoid unnecessary migration barriers if adoption grows quickly. See [SCALING.md](SCALING.md).

---

## 5. Production architecture doctrine

SUAS remains a **scalable modular monolith**:

```text
Clients
  |
  v
Stateless SUAS application tier
  |
  +--> PostgreSQL
  |
  +--> Durable jobs/workers
          |
          +--> SMS / Email adapters
          +--> Transportation adapter(s)
          +--> Temporary shelter/room adapter(s)
          +--> Food-support adapter(s)
          +--> Peer-support adapter(s)
          `--> Manual coordination adapters
```

Rules:

1. Do not introduce microservices without measured need and a released spec change.
2. Correctness-critical state must not depend on one app process.
3. Production-critical async work must be durable across worker restart.
4. External services are capability ports with replaceable adapters.
5. Manual providers are first-class; API availability is not required for a valid Resource/Service Provider.
6. Provider-specific status never replaces canonical Service Request/Fulfillment state.
7. External mutations use idempotent Fulfillment Attempts and ambiguous outcomes are reconciled.
8. Growing APIs are bounded/paginated.
9. Scale/resilience are verified with load/failure evidence, not assumed from architecture diagrams.

See [ARCHITECTURE.md](ARCHITECTURE.md), [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [SCALING.md](SCALING.md), [RESILIENCE.md](RESILIENCE.md).

---

## 6. Referenced MVP visual authority

The existing MVP at `https://suasqrf.org/app/` is the production **visual and interaction reference**.

Production should preserve its recognizable action-first hierarchy, low cognitive load, role clarity, resource visibility, responder/QRF immediacy, and mobile-first navigation.

The MVP reference does **not** override canonical security, auth, consent, privacy, accessibility, safety, or domain semantics. Required production divergences are explicit in [MVP_REFERENCE.md](MVP_REFERENCE.md).

Visual conformance is a readiness gate, not an informal design preference.

---

## 7. Provider-neutral service fulfillment

The MVP categories map to capability ports:

| Category | Port | Provider choice |
|---|---|---|
| `TRANSPORTATION` | `TransportationPort` | D-017 `DECISION_PENDING` |
| `SHELTER` | `TemporaryShelterPort` | D-018 `DECISION_PENDING` |
| `FOOD` | `FoodSupportPort` | D-019 `DECISION_PENDING` |
| `PEER_SUPPORT` | `PeerSupportPort` | D-020 if external; internal/manual path valid |

Specific rides, rooms, food, and peer-support vendors are **not** architecture. They are conforming adapters/configuration choices.

Integration modes may include `API`, `WEBHOOK`, `DEEP_LINK`, `PHONE`, `EMAIL`, `MANUAL_COORDINATION`, `NONE`.

See [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md) and [APIS.md](APIS.md).

---

## 8. Readiness gates

All are currently `NOT_READY`:

- `AUTH`
- `CONSENT`
- `CHECK-IN`
- `COORDINATION`
- `EXTERNAL_FULFILLMENT`
- `UI_CONFORMANCE`
- `SAFETY`
- `PRIVACY`
- `SCALE`
- `RESILIENCE`
- `OPERATIONS`
- `REPORTING`

See [STATUS.md](STATUS.md) and [TESTING.md](TESTING.md).

---

## 9. Document index

### Product / authority

| File | Purpose |
|---|---|
| [PRODUCT.md](PRODUCT.md) | Mission, roles, categories, loop, non-goals |
| [MVP_REFERENCE.md](MVP_REFERENCE.md) | Referenced MVP visual/interaction conformance contract |
| [GLOSSARY.md](GLOSSARY.md) | Canonical terminology |
| [STATUS.md](STATUS.md) | Spec/pilot/production readiness |
| [VERSIONING.md](VERSIONING.md) | Artifact lifecycle/versioning |
| [ROADMAP.md](ROADMAP.md) | Ordered Spec-Driven Development path to release |
| [DECISIONS.md](DECISIONS.md) | Open decisions; no guessing |
| [AGENTS.md](AGENTS.md) | Cross-repo/agent governance |

### Architecture / integrations / scale

| File | Purpose |
|---|---|
| [ARCHITECTURE.md](ARCHITECTURE.md) | Scalable modular monolith and module boundaries |
| [DOMAIN_MODEL.md](DOMAIN_MODEL.md) | Entities/ownership/lifecycle/authz |
| [DATA_MODEL.md](DATA_MODEL.md) | Logical PostgreSQL schema |
| [EVENT_MODEL.md](EVENT_MODEL.md) | Domain/Audit Events |
| [API.md](API.md) | SUAS Plane A resource/domain contract |
| [APIS.md](APIS.md) | Plane A minimum + Plane B capability inventory |
| [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md) | Provider-neutral fulfillment ports/adapters/manual fallback |
| [SCALING.md](SCALING.md) | Scale bands, statelessness, durable work, DB/API/load rules |
| [RESILIENCE.md](RESILIENCE.md) | Failure/degradation/retry/reconciliation/recovery contract |

### Domain

[AUTH.md](AUTH.md), [CONSENT.md](CONSENT.md), [CHECKINS.md](CHECKINS.md), [SUPPORT_SIGNALS.md](SUPPORT_SIGNALS.md), [SAFETY.md](SAFETY.md), [TRUSTED_CIRCLE.md](TRUSTED_CIRCLE.md), [CASES.md](CASES.md), [DISPATCH.md](DISPATCH.md), [RESOURCES.md](RESOURCES.md), [REFERRALS.md](REFERRALS.md), [FULFILLMENT.md](FULFILLMENT.md), [FOLLOWUP.md](FOLLOWUP.md), [SETTLEMENT.md](SETTLEMENT.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md), [NOTIFICATIONS.md](NOTIFICATIONS.md).

### Security / operations / verification

[SECURITY.md](SECURITY.md), [PRIVACY.md](PRIVACY.md), [COMPLIANCE.md](COMPLIANCE.md), [ONBOARDING.md](ONBOARDING.md), [ADMIN.md](ADMIN.md), [PILOT.md](PILOT.md), [ANALYTICS.md](ANALYTICS.md), [TESTING.md](TESTING.md), [DEPLOYMENT.md](DEPLOYMENT.md), [OPERATIONS.md](OPERATIONS.md), [INCIDENT_RESPONSE.md](INCIDENT_RESPONSE.md).

### Process

[CONTRIBUTING.md](CONTRIBUTING.md), [SPEC-001.md](SPEC-001.md), [FRICTION.md](FRICTION.md), [CHANGELOG.md](CHANGELOG.md), [SPEC_AUDIT.md](SPEC_AUDIT.md), `CODEOWNERS`.

---

## 10. Engineer rules

1. Read PRODUCT, GLOSSARY, README, and relevant released specs before implementation.
2. Treat draft specs as design work, not shipping authority.
3. Cite spec file/section/version in implementation PRs.
4. Do not invent partners, providers, county/VA/Medi-Cal agreements, legal status, signal weights, staffing, performance SLOs, or recovery objectives.
5. Do not implement generative AI for safety-critical decisions.
6. Do not encode provider names/SDK types into domain modules.
7. Do not treat an external provider callback as permission to bypass canonical transitions or confirmation.
8. Preserve the referenced MVP experience unless a documented production constraint requires divergence.
9. Run the applicable critical/readiness suites before claiming a domain or release ready.
10. Do not claim HIPAA compliance; D-006 remains open.
11. Do not write production application code in this specification repository.

---

## 11. Epistemic labels

| Label | Meaning |
|---|---|
| `OBSERVED` | Directly evidenced |
| `INFERRED` | Derived from stated rules; not independently evidenced |
| `SPECULATIVE` | Possible, not decided |
| `NOT_COMPUTABLE` | Cannot be determined from available information |
| `DECISION_PENDING` | Open decision; do not guess |
| `FUTURE` | Explicitly outside MVP scope |

---

## 12. Quality rules

- Explicit, testable, non-redundant, internally linked.
- Preserve canonical terminology and separate non-interchangeable concepts.
- Preserve MVP product identity without copying unsafe/incorrect prototype behavior.
- No unsupported clinical/compliance claims.
- No automated emergency dispatch.
- No vendor-specific domain lock-in.
- No pilot-scale architectural assumptions where a simple scalable seam is available.
- Complexity must solve measured or clearly foreseeable production ambiguity; do not add distributed-system machinery for its own sake.
