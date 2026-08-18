# SPEC-016.md — First released specification cut checklist

**Status:** `BLOCKED_BY_SPEC_001_THROUGH_SPEC_015`  
**Stack version:** `0.1.0` / `draft` until owner release action  
**Owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-016

This file defines release assembly. It does **not** release anything and agents must not mark it complete on behalf of the owner.

---

## 1. Objective

Create the first implementation-authoritative specification cut only after prerequisite stages are owner-accepted and every release-relevant open decision is decided or safely deferred under SPEC-015.

A release is a named/pinned contract set. It is not “whatever is on main.”

---

## 2. Prerequisite gate

Before release assembly:

- [ ] SPEC-001 accepted.
- [ ] SPEC-002 accepted.
- [ ] SPEC-003 accepted.
- [ ] SPEC-004 accepted.
- [ ] SPEC-005 accepted.
- [ ] SPEC-006 accepted.
- [ ] SPEC-007 accepted.
- [ ] SPEC-008 accepted.
- [ ] SPEC-009 accepted.
- [ ] SPEC-010 accepted.
- [ ] SPEC-011 accepted.
- [ ] SPEC-012 accepted.
- [ ] SPEC-013 accepted.
- [ ] SPEC-014 accepted.
- [ ] SPEC-015 accepted with release decision ledger.

Any intentionally omitted stage requires an explicit owner-approved governance change to ROADMAP, not an implicit skip.

---

## 3. Release manifest

The release cut must record:

```text
release_version
release_date_pt
owner
source_commit_sha
artifact_path
artifact_version
artifact_lifecycle = released
runtime_artifact_versions where applicable
release decision ledger reference
known safe deferrals / unavailable features
supersedes
```

At minimum the manifest includes every artifact required to implement the enabled release surface. A file left `draft`/`accepted` is not implementation authority merely because another file links to it.

---

## 4. Runtime artifact pins

Where enabled, pin:

- QuestionnaireVersion;
- Support Signal `signal_version`;
- Event `schema_version`;
- API contract version (`/api/v0` for this stack if released unchanged);
- Consent template version(s);
- Notification template version(s);
- approved safety-copy artifact/version/reference;
- provider adapter configuration decision references for enabled external capabilities;
- D-021/D-023 release performance plan reference;
- D-024 recovery plan reference;
- D-025 aggregate reporting privacy policy reference if affected reports are enabled.

Do not pin `UNRELEASED_FIXTURE` as production authority.

---

## 5. Feature-availability manifest

For each potentially optional/deferred surface, record one of:

`ENABLED`, `MANUAL_ONLY`, `INFORMATION_ONLY`, `UNAVAILABLE`, `FUTURE`.

At minimum:
- SMS;
- email;
- geocoding/maps;
- external transportation adapter;
- external shelter/room adapter;
- external food adapter;
- external peer-support adapter;
- aggregate report slices affected by D-025;
- future product categories shown for MVP visual continuity.

No feature may appear operational in UI/admin if the release manifest marks it unavailable/future.

---

## 6. Decision ledger verification

For D-001–D-025:

- [ ] each release-relevant item is `DECIDED` or `DEFERRED_FOR_RELEASE`;
- [ ] deferred items have a safe feature boundary;
- [ ] no row says “implementation default”;
- [ ] provider decisions select adapters/configuration, not domain types;
- [ ] no capacity/SLO/RTO/RPO/privacy threshold is inferred from prototype or code;
- [ ] D-006/D-013 evidence is represented without unsupported compliance claims.

---

## 7. Cross-artifact consistency check

Before release:

- [ ] canonical terminology matches GLOSSARY;
- [ ] state machines align across PRODUCT/CASES/DISPATCH/FULFILLMENT/API/DATA_MODEL/TESTING;
- [ ] Settlement multi-cycle semantics align across CASES/SETTLEMENT/API/DOMAIN_MODEL/DATA_MODEL;
- [ ] signal computation/effective projection aligns across CHECKINS/SUPPORT_SIGNALS/EVENT_MODEL/DATA_MODEL;
- [ ] command idempotency/event identity semantics align across API/EVENT_MODEL/DATA_MODEL/ARCHITECTURE;
- [ ] provider-neutral ports/status/privacy align across PROVIDER_INTEGRATIONS/APIS/FULFILLMENT/CONSENT/SECURITY/ADMIN;
- [ ] notification logical-send/delivery semantics align across NOTIFICATIONS/DATA_MODEL/TESTING;
- [ ] MVP visual/QRF semantics align with PRODUCT/AUTH/SAFETY/CONSENT and do not overpromise availability/proximity;
- [ ] scale/resilience/test/operations gates use the same workload/failure vocabulary;
- [ ] no stale SPEC numbering/open-decision range remains.

---

## 8. Lifecycle update

Only the owner may execute release lifecycle changes.

A valid release updates consistently:
- [STATUS.md](STATUS.md);
- [VERSIONING.md](VERSIONING.md);
- [CHANGELOG.md](CHANGELOG.md);
- release manifest;
- affected artifact metadata/lifecycle.

Implementation authority changes to `RELEASED_FOR_IMPLEMENTATION` **only for the named released artifacts/version**.

Unreleased later/future artifacts remain non-authoritative.

---

## 9. Post-release handoff

After SPEC-016 release:

1. `SUAS` implementation PRs cite released file/section/version/artifact pins.
2. SPEC-017 compares implementation against the released cut.
3. Implementation gaps return to specs; code does not redefine canon.
4. SPEC-018 evaluates actual launch readiness from test/load/failure/restore/operations evidence.

Release does not mean production-ready. It means implementation-authoritative.

---

## 10. Non-goals

- merging draft PR #4 automatically;
- marking readiness gates READY;
- choosing undecided providers/infrastructure;
- treating acceptance as release;
- treating release as launch approval;
- releasing unreviewed implementation defaults.

---

## 11. Exit criteria

SPEC-016 exits only when the owner creates a complete release manifest, verifies prerequisite acceptance/decision ledger/cross-artifact consistency, and explicitly changes the named artifact lifecycle to `released`.

Until then: `IMPLEMENTATION_AUTHORITY = NOT_YET_RELEASED`.
