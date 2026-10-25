# Wossol — Offline Merchant First-Read Runner V0.1
Date: 2026-10-25
Status: READY TO RUN; 0 merchant participants
## Goal
Convert the previously prepared blinded four-stimulus first-read pilot into a self-contained Arabic browser tool usable offline, without inventing any research responses.
## Source
Brand Master Instructions V2; Project Entrypoint; PROJECT_STATUS; PROJECT_INDEX; DECISION_LOG; R2_WORDMARK_MERCHANT_PERCEPTION_TEST_PROTOCOL_V0.1.md; MERCHANT_FIRST_READ_PILOT_STIMULI_AND_REAL_UI_GATE_V0.1.md.
## Implemented
- Self-contained HTML with embedded M01-M04 PNG stimuli (no external images, scripts, CSS or network).
- Consent gate, anonymous participant codes P01-P10, language and merchant-stage tags.
- Balanced order from BALANCED_ORDERS.csv, separate three-second first exposure and hidden unprompted response field.
- Follow-up company-category, adjective and notes fields, pairwise comparisons, delayed-recall fields.
- One-participant-at-a-time CSV export, UTF-8 with BOM. No automatic storage or transmission.
- Arabic facilitator instructions. ZIP and HTML delivered as conversation downloads, not asserted to be persisted in GitHub.
## Verification
Node.js syntax check passed. Headless Chromium Playwright UI smoke test passed: 10 participant choices, consent gate, first exposure, automatic three-second hiding, first-read capture and follow-up image reveal. The UI screenshot is a test-run screenshot of the pilot tool, not a production Wossol dashboard or merchant research result.
## Methodology limits
The qualitative pilot needs 6-10 real consenting merchants. 0 responses at authoring time. Counterbalancing and 3-second exposure improve consistency but do not establish statistical significance. The facilitator must wait 2-3 minutes for delayed recall; this delay is not enforced by the software. Arabic specimens are typeface-based prototypes, not proprietary finished marks.
## Recommendation
RUN the pilot before any further minor logo changes. Compare unprompted correct name reading, recorded misreads, trust/approachability explanations and delayed recall. Do not claim winner or brand approval before reviewing real responses. Keep C and Arabic B as provisional directions only.
## Next
Collect anonymized exported CSV files, combine and analyze evidence against protocol. If pilot cannot be recruited, continue product UI fit testing without pretending to have consumer evidence.
## Decision
No identity element approved.
