# SPEC-009.md — Owner review worksheet: provider-neutral service fulfillment

**Status:** `BLOCKED_BY_SPEC_004_THROUGH_SPEC_007`  
**Stack version:** `0.1.0` / `draft`  
**Owner:** `@scrimshawlife-ctrl`  
**Roadmap:** [ROADMAP.md](ROADMAP.md) SPEC-009  
**Required:** [PROVIDER_INTEGRATIONS.md](PROVIDER_INTEGRATIONS.md), [APIS.md](APIS.md), [FULFILLMENT.md](FULFILLMENT.md), [CONSENT.md](CONSENT.md), [SECURITY.md](SECURITY.md), [ADMIN.md](ADMIN.md)

Preflight only. No provider is selected and no lifecycle advances.

---

## 1. Objective

Accept provider-neutral fulfillment semantics for transportation, temporary shelter/rooms, food support, and peer support so provider choice/configuration can change without rewriting SUAS domain state.

---

## 2. Owner checklist

### Capability ports
- [ ] `TransportationPort` semantics are provider-neutral.
- [ ] `TemporaryShelterPort` means temporary shelter/accommodation, not permanent housing.
- [ ] `FoodSupportPort` supports both transactional and catalog/referral-only providers.
- [ ] `PeerSupportPort` supports external or local/manual QRF paths and is not therapy/clinical care.

### Integration modes
- [ ] `API`, `WEBHOOK`, `DEEP_LINK`, `PHONE`, `EMAIL`, `MANUAL_COORDINATION`, `NONE` cover MVP provider realities without assuming ecommerce APIs.
- [ ] API-backed providers are not automatically preferred over manual providers.
- [ ] Manual Adapter exists as a first-class capability path.

### Router / policy
- [ ] Provider Router operates above adapters and does not own Service Request state.
- [ ] Routing uses only accepted operational inputs such as capability, coverage, tenant config, health, capacity/availability, explicit priority, responder choice.
- [ ] Routing does not infer clinical eligibility or undocumented qualification.
- [ ] Routing policy/config changes that alter disclosure destination are auditable/versioned/configurable.

### Normalization
- [ ] ProviderOffer is a bounded SUAS-owned normalized shape.
- [ ] Provider-specific statuses remain adapter-local.
- [ ] Normalized provider attempt status remains distinct from Service Request/Fulfillment state.
- [ ] Provider completion is evidence, not automatic SUAS confirmation/closure.

### External mutation safety
- [ ] Every provider mutation belongs to one FulfillmentAttempt with stable idempotency identity.
- [ ] Duplicate job/user retry cannot duplicate ride/room/food/peer dispatch for the same attempt.
- [ ] Deliberate provider switch/reroute creates a new attempt.
- [ ] Ambiguous timeout becomes `PROVIDER_UNKNOWN` and reconciles before duplicate-risk retry.
- [ ] Concurrent responders/workers cannot accidentally initiate the same logical external attempt twice.

### Webhooks
- [ ] Webhooks are adapter-local ingress.
- [ ] Authentication/signature verification required.
- [ ] Duplicate/out-of-order webhook handling is safe.
- [ ] Webhooks cannot authorize new disclosure or bypass canonical state transitions.
- [ ] Persisted callback data is minimized/normalized.

### Privacy / consent
- [ ] Provider projection is minimum necessary and purpose-scoped.
- [ ] Full Check-In, full Case Notes, Trusted Circle, unrelated requests, medical history, SSN/service records are excluded by default.
- [ ] Location/contact/accessibility/destination fields require accepted purpose/basis when needed.
- [ ] Revocation stops future disclosures where workflow permits.

### Resilience / replacement
- [ ] Adapter health states and degraded behavior are explicit.
- [ ] Unavailable API can route to another conforming adapter/manual path where policy permits.
- [ ] Provider outage does not close/erase Service Request.
- [ ] One adapter can be replaced by another without changing canonical Service Request/Fulfillment contracts.

### Administration
- [ ] Provider decisions D-017–D-020 remain configuration/partner decisions, not domain architecture.
- [ ] Provider secrets never appear in Resource/domain/client-visible config.
- [ ] Admin may expose normalized health/config state without leaking secrets/vendor-domain statuses.

---

## 3. Common adapter conformance suite

Every enabled adapter must prove, as applicable:

1. normalized search/availability/offer behavior;
2. stable external mutation idempotency;
3. finite timeout and bounded retry/backoff;
4. rate-limit handling;
5. unknown-outcome reconciliation;
6. authenticated/deduplicated callback handling;
7. minimum-necessary disclosure projection;
8. canonical status normalization;
9. cancellation semantics when supported;
10. degradation/manual fallback behavior;
11. no provider SDK types crossing the adapter boundary.

Capabilities a provider does not support must be reported unsupported, not faked.

---

## 4. Open decisions

D-017 transportation adapter(s), D-018 shelter/room adapter(s), D-019 food adapter(s), D-020 external peer-support adapter remain `DECISION_PENDING` until separately closed.

SPEC-009 may accept the provider-neutral contract while these actual provider choices remain open, provided unavailable capabilities are explicitly non-shippable/manual-only until configured.

---

## 5. Non-goals

No vendor selection, pricing/eligibility product law, payment checkout, clinical qualification, or implementation SDK choice.

---

## 6. Exit criteria

SPEC-009 may be accepted only after dependencies are accepted and the owner confirms all enabled-provider seams can be tested/replaced without domain changes.

**Implementation handoff:** none until SPEC-016 release.
