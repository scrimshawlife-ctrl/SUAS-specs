# SPEC-008.md — Owner acceptance record: MVP visual and interaction conformance

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the referenced MVP as the recognizable visual and interaction source of truth for v0.1 implementation.

Accepted requirements:

- Preserve the action-first hierarchy, `TAKE ACTION`, `I NEED SUPPORT`, `I WANT TO SERVE`, QRF deploy/search/contact/cancel flow, Immediate Resources, responder On Duty/QRF dashboard, Quick Resource Share, simple Home/Chat navigation, and distinct admin surface.
- Keep QRF truthful: production semantics are a `PEER_SUPPORT` coordination request, not guaranteed proximity, GPS use, immediate notification, or guaranteed dispatch.
- Call/Message controls require a real authorized contact path; degraded/no-availability states must remain low-friction.
- Resource display labels map to canonical categories; future categories may appear only as visibly non-operational/information-only continuity elements.
- Replace contradictory prototype auth copy such as `No email` with the released passwordless identity contract.
- Official safety/crisis copy must come from an approved later decision; prototype statistics/clinical claims are not inherited.
- Accessibility target is WCAG 2.2 AA and visual-regression fixtures must detect hierarchy/navigation/product drift rather than demand pixel identity.
- Production hardening must not turn SUAS into a dense generic SaaS/EHR console.
