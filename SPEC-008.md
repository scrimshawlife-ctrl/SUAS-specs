# SPEC-008.md — Owner review worksheet: MVP visual and interaction conformance

**Status:** `BLOCKED_BY_SPEC_001_SPEC_002_SPEC_007`  
**Stack version:** `0.1.0` / `draft`  
**Owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-008  
**Required:** [MVP_REFERENCE.md](MVP_REFERENCE.md), [PRODUCT.md](PRODUCT.md), [ONBOARDING.md](ONBOARDING.md), [AUTH.md](AUTH.md), [SAFETY.md](SAFETY.md), [RESPONDER_WORKFLOWS.md](RESPONDER_WORKFLOWS.md)

Preflight only. No lifecycle advancement before dependencies are accepted. Agents do not tick owner checkboxes.

---

## 1. Objective

Accept the referenced MVP as the recognizable production visual/interaction source of truth while explicitly rejecting prototype behaviors/copy that conflict with canonical auth, consent, safety, privacy, accessibility, provider-truth, or domain semantics.

Reference observation for this preflight: 2026-08-18 PT.

---

## 2. Owner checklist

### Product hierarchy
- [ ] `TAKE ACTION` remains a primary landing hierarchy.
- [ ] `I NEED SUPPORT` and `I WANT TO SERVE` remain immediately recognizable primary choices.
- [ ] Veteran and Responder/QRF role recognition remains fast.
- [ ] Production hardening does not turn the product into a dense generic SaaS/EHR dashboard.

### Veteran support / QRF
- [ ] QRF remains a dominant support action.
- [ ] Deploy/request → searching/pending → contact/cancel interaction remains recognizable.
- [ ] Production QRF creates a truthful `PEER_SUPPORT` coordination request rather than claiming guaranteed dispatch.
- [ ] No guarantee of responder proximity/availability is shown without actual evidence.
- [ ] Continuous GPS is not required; location use remains purpose-scoped/authorized.
- [ ] Call/Message controls appear only when an authorized actual contact path exists.
- [ ] No-availability/degraded state exists and remains low-friction.

### Immediate resources / safety
- [ ] Immediate-resource placement remains prominent.
- [ ] Exact safety/crisis copy follows SAFETY/D-012 rather than prototype wording by default.
- [ ] Prototype statistics/clinical claims are not inherited automatically.

### Resource categories
- [ ] Food, Transportation, temporary Shelter/Housing display, and Peer/QRF support map to canonical MVP categories.
- [ ] Counseling, Activities/Community, Job Training may preserve visual continuity only as `COMING_SOON`/information-only unless later promoted.
- [ ] Visual reference labels do not silently create unreleased Service Request categories.

### Resource detail
- [ ] Category header, scannable entries, direct contact actions, back navigation, county/coverage context remain recognizable.
- [ ] Resource contact/availability facts come from current verified data, not hard-coded prototype truth.
- [ ] Long lists remain mobile-friendly through progressive loading/pagination/virtualization without losing browse simplicity.

### Responder/QRF dashboard
- [ ] On-duty/availability remains a primary responder state/control.
- [ ] Active needs/alerts remain immediate work.
- [ ] Quick Resource Share remains prominent for released resource capabilities.
- [ ] Home/Chat remain simple persistent navigation concepts.
- [ ] Placeholder metrics are not shown as production facts unless precisely defined and backed by data.

### Admin
- [ ] Admin remains visually distinct.
- [ ] Informal prototype labels such as `GOD MODE` may evolve to explicit canonical admin terminology.
- [ ] Admin UI makes global vs org-scoped authority clearer than the prototype.

### Accessibility / regression
- [ ] WCAG 2.2 AA target accepted.
- [ ] Critical controls work with keyboard, text zoom/reflow, screen-reader naming, visible focus, large mobile targets.
- [ ] Visual regression fixtures cover landing, enrollment, veteran support, QRF pending/degraded, resources, responder dashboard, alerts, chat, admin, mobile nav.
- [ ] Fixtures record reference observation/revision and approved divergences.

---

## 3. Mandatory production divergences

Owner should explicitly confirm these are not considered undesirable visual drift:

1. Replace prototype `No email` enrollment copy with the accepted passwordless email/phone identity flow.
2. Replace guaranteed `near you / notified immediately` QRF claims with truthful request/availability/contact state.
3. Do not require GPS merely to preserve prototype wording.
4. Exact crisis/safety text comes from accepted safety copy, not prototype content.
5. Prototype category cards for unreleased domains may remain visual/informational but cannot create hidden workflows.
6. Prototype mission/statistical/clinical language is not automatically a canonical production claim.
7. Informal admin terminology may be normalized to canonical role language.

---

## 4. Non-goals

No pixel-perfect clone, CSS/framework freeze, source-code cloning, hidden domain-state creation, clinical-claim acceptance, or lifecycle advancement.

---

## 5. Exit criteria

SPEC-008 may be owner-accepted only after dependencies are accepted and the owner confirms:
- required surfaces/hierarchy;
- mandatory production divergences;
- category mapping;
- accessibility target;
- visual regression fixture contract;
- truthful QRF availability semantics.

**Implementation handoff:** none until SPEC-016 release.
