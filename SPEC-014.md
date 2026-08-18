# SPEC-014.md — Owner acceptance record: controlled pilot and analytics

**Status:** `accepted`  
**Accepted:** `2026-08-18` PT  
**Stack version:** `0.1.0`  
**Owner:** `@scrimshawlife-ctrl`

The owner accepts the controlled-pilot and operational-analytics contracts.

Accepted requirements:

- The controlled Santa Clara County pilot remains approximately 25–50 enrolled veterans unless explicitly changed later.
- Traction/demand above cohort capacity is not PilotEnrollment and does not silently expand the pilot.
- Pilot cohort size is not a technical capacity ceiling.
- Partner/staffing/counsel decisions remain required before actual pilot operation or coverage/compliance claims.
- Enabled provider adapters must pass conformance or the capability remains manual/unavailable explicitly.
- Confirmed fulfillment means canonical Service Request `CONFIRMED`, not merely provider/Fulfillment `COMPLETED`; pending confirmation is reported separately.
- Referral completion is not Fulfillment.
- Rates require explicit numerator, denominator, cohort/window, state treatment, tenant scope, and source.
- No suicides-prevented, lives-saved, clinical-efficacy, diagnosis/recovery, suicide-prediction, or unsupported causal-health metric is permitted.
- Traction/registrations/throughput are not health-efficacy evidence.
- D-025 owns production small-cell/sensitive aggregate reporting policy; no threshold is invented.

This acceptance does not authorize a live pilot. Pilot operation still requires the later decision closures and SPEC-017/018 evidence defined by the release manifest.
