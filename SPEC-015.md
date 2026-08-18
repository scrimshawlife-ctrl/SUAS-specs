# SPEC-015.md — Owner review worksheet: release-blocking decisions and safe deferrals

**Status:** `BLOCKED_BY_RELEVANT_SPEC_001_THROUGH_SPEC_014`  
**Stack version:** `0.1.0` / `draft`  
**Owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-015  
**Authority:** [DECISIONS.md](DECISIONS.md) plus affected accepted specs

This worksheet classifies what must be decided for a target production release and what may remain open only when the corresponding capability is explicitly unavailable/deferred. It does not itself close any decision.

---

## 1. Governing rule

A decision may remain open at SPEC-016 only if:

1. the affected capability is not required by the target release; and
2. the product/admin/UI clearly exposes it as unavailable/future/manual-only rather than guessing a value/provider; and
3. no accepted safety/privacy/security/operations contract depends on that missing value; and
4. the deferral is recorded in the release cut.

`DECISION_PENDING` must never become an implicit implementation default just to ship.

---

## 2. Release-decision matrix

| ID | Topic | First production release treatment |
|---|---|---|
| D-001 | Production hosting/cloud | **MUST CLOSE** for production deployment |
| D-002 | Auth provider/in-house implementation | **MUST CLOSE** for enabled production auth |
| D-003 | SMS provider | **CONDITIONAL** — must close if SMS/phone OTP/operational SMS enabled; otherwise channel explicitly `UNAVAILABLE` |
| D-004 | Email provider | **MUST CLOSE** if email is the required production veteran/admin auth/notification path; at least one accepted usable passwordless delivery path must exist |
| D-005 | Database hosting | **MUST CLOSE** for production |
| D-006 | Legal/HIPAA applicability/classification | **MUST CLOSE TO THE EXTENT REQUIRED BY COUNSEL/OPERATIONS** before pilot legal/compliance posture is represented; no compliance claim may ship while unknown |
| D-007 | Retention durations | **MUST CLOSE** for production operational data, events, backups, idempotency, and deletion handling required by accepted privacy/security contracts |
| D-008 | Pilot partner organizations | **MUST CLOSE** for named/operated pilot partner participation; not needed for a non-operating demo |
| D-009 | Staffing/coverage hours | **MUST CLOSE** for pilot operation; exact limitations must be truthful and no 24/7 overclaim |
| D-010 | Service funding | **MAY DEFER** because funding/billing remains `FUTURE`; fulfillment may operate with no funding workflow |
| D-011 | Support Signal scoring rules | **MUST CLOSE OR SIGNAL FEATURE MUST NOT SHIP AS PRODUCTION**; unreleased fixtures are not production thresholds |
| D-012 | Approved safety/crisis-resource copy | **MUST CLOSE** for production red-state/immediate-resource UI required by accepted SAFETY contract |
| D-013 | Counsel review of compliance register | **MUST CLOSE** before operating the controlled pilot |
| D-014 | Geocoding/maps requirement | **MAY DEFER** if no geocoding/maps capability is required/enabled; county/manual coverage remains accepted path |
| D-015 | Full Case Note veteran visibility later | **MAY DEFER** by retaining accepted MVP default that full Case Notes are not veteran-visible |
| D-016 | Identity proofing beyond MVP default | **MAY DEFER** by retaining accepted self-attest + working passwordless-channel MVP default, subject to D-006/counsel |
| D-017 | Transportation external adapter | **CONDITIONAL** — close if external adapter enabled; otherwise Manual/Referral/Information path must be explicit |
| D-018 | Shelter/room external adapter | **CONDITIONAL** — same |
| D-019 | Food external adapter | **CONDITIONAL** — same |
| D-020 | External peer-support adapter | **MAY DEFER/CONDITIONAL** — internal/manual QRF path may satisfy release if accepted/operable |
| D-021 | Release workload/capacity envelope | **MUST CLOSE** for target production release/load plan |
| D-022 | Durable job/queue implementation | **MUST CLOSE** for production-critical async work |
| D-023 | Performance SLOs/alert thresholds | **MUST CLOSE** for production operations/readiness appropriate to target release |
| D-024 | RTO/RPO / backup-restore objectives | **MUST CLOSE** for production resilience/readiness |
| D-025 | Aggregate reporting privacy/small-cell policy | **MUST CLOSE FOR AFFECTED AGGREGATE REPORTS**; otherwise small/sensitive report slices remain unavailable |

This matrix is release planning, not legal advice. D-006/D-013 and any legal obligations require appropriate counsel evidence.

---

## 3. Owner checklist — mandatory production platform decisions

- [ ] D-001 hosting selected/configured.
- [ ] D-002 production auth implementation selected.
- [ ] At least one usable production passwordless delivery path has its provider/implementation decision closed.
- [ ] D-005 production DB hosting selected.
- [ ] D-007 retention policy accepted.
- [ ] D-021 release workload envelope accepted.
- [ ] D-022 durable job execution selected/configured.
- [ ] D-023 operational SLO/alert policy accepted.
- [ ] D-024 recovery objectives accepted.

---

## 4. Owner checklist — pilot operation

- [ ] D-006 legal classification/posture closed as required for operation and claims.
- [ ] D-008 participating partners named/approved for operating pilot.
- [ ] D-009 staffing/coverage hours accepted and reflected truthfully in UI/ops.
- [ ] D-013 counsel register review complete.
- [ ] D-011 production signal rules accepted or production signal feature explicitly unavailable.
- [ ] D-012 approved production safety copy accepted.

---

## 5. Owner checklist — enabled external fulfillment

For each capability enabled through an external adapter:

- [ ] corresponding D-017/D-018/D-019/D-020 decision is closed;
- [ ] adapter passes SPEC-009 conformance suite;
- [ ] secrets/configuration/webhook auth are production-ready;
- [ ] consent/provider projection reviewed;
- [ ] alternate/manual/degraded behavior is explicit;
- [ ] no provider-specific domain state introduced.

Capabilities without a closed external-provider decision remain manual/referral/information-only/unavailable according to accepted specs.

---

## 6. Owner checklist — safe deferrals

Each deferred decision has a written release boundary stating:

- [ ] why it is not required for this release;
- [ ] which feature/path remains unavailable or uses an already accepted default/manual path;
- [ ] how UI/admin communicates that state truthfully;
- [ ] how tests prove no hidden implementation default was invented;
- [ ] what future roadmap stage/decision will revisit it.

---

## 7. D-025 reporting rule

If D-025 remains open at release:

- aggregate reports that can expose small/sensitive slices remain unavailable or only expose demonstrably non-sensitive aggregates not dependent on an undecided suppression policy;
- no invented threshold is hard-coded as approved policy;
- raw veteran-level access remains governed by normal auth/privacy/export rules and is not a workaround for aggregate-report restrictions.

---

## 8. Release decision record

Before SPEC-016, produce a release decision ledger with one row per D-001–D-025:

```text
ID
status = DECIDED | DEFERRED_FOR_RELEASE
release_version
owner
accepted decision/default/unavailable boundary
spec references
evidence/config reference
feature impact
review date
```

No row may simply say “use implementation default.”

---

## 9. Non-goals

- closing decisions automatically;
- choosing vendors in this worksheet;
- legal advice;
- converting FUTURE features into MVP;
- bypassing safety/privacy requirements through feature flags;
- treating manual service coordination as inferior/non-production merely because an external API decision is deferred.

---

## 10. Exit criteria

SPEC-015 may be owner-accepted when every release-relevant D-001–D-025 item is either:

1. explicitly decided with evidence/config references; or
2. explicitly deferred with a safe unavailable/manual/default boundary permitted by accepted specs.

Only then may SPEC-016 assemble a first released specification cut.

**Implementation authority remains `NOT_YET_RELEASED` until SPEC-016.**
