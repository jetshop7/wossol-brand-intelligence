# Wossol Merchant First-Read Runner V0.2 — QA and Protocol Corrections
Date: 2026-10-25
Status: QA-PASSED / READY FOR REAL PARTICIPANTS; n=0

## Purpose
Audit and correct the V0.1 offline merchant test runner before gathering any real responses. This is validation tooling, not design approval.

## Source of truth and context
Read Master Instructions V2, ChatGPT Project Entrypoint, README, PROJECT_STATUS, PROJECT_INDEX, DECISION_LOG, the existing pilot protocol and V0.1 runner report. Brand Wossol/وصول approved as working name, not legally cleared. No identity direction approved.

## Confirmed defects in V0.1
1. The queue was always Latin-first and Arabic-second for every participant. This confounded script block order.
2. The next() function checked index >= queue.length before index===4; therefore it skipped the Arabic pairwise comparison after the fourth specimen. The resulting CSV lacked Arabic comparison fields.
3. First-read text could be left empty without explicit missing-response labeling.
4. Delayed recall had no enforced two-minute minimum.

## V0.2 corrections
- Odd participant IDs P01/P03/P05/P07/P09 receive Latin block first; even P02/P04/P06/P08/P10 receive Arabic block first. Within each block, original candidate order remains counterbalanced by the existing BALANCED_ORDERS.csv.
- The comparison branch executes at index 2 and 4, with block identity derived from the immediately preceding specimen.
- First-read field cannot be skipped empty; facilitator may explicitly enter لم يقرأ.
- Delayed recall is gated until >=120 seconds and the actual elapsed seconds are saved.
- The output remains one anonymous CSV per participant, local-only, with no network calls.

## Verification performed
Full Chromium/Playwright smoke test with fake test-only inputs (not participant responses) for P01 and P02, including all four 3-second timed stimuli, two comparison stages, blank first-read rejection, two-minute delayed recall gating, and record fields for both languages. PASS. These are software tests, NOT empirical brand results. Pilot participants recorded: 0.

## Deliverables
Self-contained WOSSOL_OFFLINE_PILOT_RUNNER_V02.html, README_V02_AR.md, and check_v02.py packaged in WOSSOL_OFFLINE_MERCHANT_PILOT_RUNNER_V02_FIXED.zip, supplied in conversation. The executable HTML and ZIP are not yet archived as repository assets.

## Critical methodology note
Use V0.2 only for new collection. If any V0.1 responses already exist, tag their runner version and inspect missing Arabic comparison fields; do not silently merge as equivalent. This small qualitative pilot cannot establish statistical market preferences. No interviews have been conducted by the assistant.

## Recommendation
TEST: Recruit 6–10 consenting merchants and capture anonymous exports. Before changing the logo again, analyze first-read errors, pairwise explanations and delayed recall. If merchants cannot be recruited immediately, proceed on other independent identity-system work rather than inventing outcomes.

## Decision status
Tooling QA passed. No brand identity approval or user design decision.
