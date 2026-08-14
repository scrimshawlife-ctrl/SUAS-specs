# CHANGELOG.md

Format: version, date, lifecycle, summary. Dates are recorded in America/Los_Angeles (PT).

---

## 0.1.0 — 2026-08-14 (PT) — `draft`

**Bootstrap of the SUAS v0.1 specification stack.**

- Created the complete markdown specification set under this repository.
- Established product identity, mission, roles, canonical loop, and non-goals.
- Established Support Case and Service Request state machines.
- Established consent as first-class grants; safety non-goals; AI policy; Medi-Cal/billing boundary as `FUTURE`.
- Established cross-repo authority: `SUAS-specs` canonical; `SUAS` implementation.
- Phase set to `SPECIFICATION_BOOTSTRAP`. Implementation authority `NOT_YET_RELEASED`. Pilot readiness `NOT_READY`.
- Open decisions recorded in [DECISIONS.md](DECISIONS.md); none guessed.

**Same-day addition (still `0.1.0` / `draft`; not a bump to 0.2.0):**

- Added [COMPLIANCE.md](COMPLIANCE.md) — compliance register (not a claim of being compliant). D-013 opened for counsel review before pilot.
- Added [APIS.md](APIS.md) — necessary-API inventory (Plane A minimum endpoints; Plane B capability ports). D-014 opened for geocoding/maps.
- Added [ONBOARDING.md](ONBOARDING.md) — admin first-run bootstrap and first-time user experience.

**Same-day SPEC-001 pass (still `0.1.0` / `draft`; not a bump to 0.2.0; not `accepted`):**

- Added [SPEC-001.md](SPEC-001.md) — owner review worksheet. SPEC-001 status `READY_FOR_REVIEW`. Agents must not self-accept.
- Opened D-015 (Case-note veteran visibility) and D-016 (Enrollment identity-proofing beyond self-attest). Both remain open. MVP defaults are `INFERRED` operational rules only.
- Specified contact-log commands: `POST /cases/{id}/commands/log-contact-attempt` and `POST /cases/{id}/commands/complete-contact`. A Case Note is not a substitute.
- Specified notification attempt shape: one Notification row; `delivery_status` transitions; retries append immutable Audit Events.
- Recorded MVP enrollment as self-attest + working email and/or phone; no VA identity API, no DD-214 upload, no in-person proofing for this pilot.
- Recorded MVP veteran visibility: own Check-Ins, own Service Request status, Settlement fields written for them, Follow-Up prompts; not full Case Notes.

**Same-day friction analysis (still `0.1.0` / `draft`; not a bump to 0.2.0; not `accepted`):**

- Added [FRICTION.md](FRICTION.md) — user and infra friction; three proposed engineering paths (consent presets + help-first; fixture-first LOCAL bootstrap; capability-port LOCAL adapters). Not implementation authority. Not a decision close.
- Proposed (not accepted) owner closes: D-014 `GEOCODE_MAPS` not required for MVP; D-012 narrow 911 + 988 display-only. D-001–D-016 remain open.

No implementation code is included. No specification artifact is `accepted` or `released`.
