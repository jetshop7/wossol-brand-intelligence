# Wossol — Merchant First-Read Pilot Stimuli and Real UI Gate V0.1
Date: 2026-10-25
Status: READY_TO_RUN (not validated)
Version: 0.1

## Objective
Stop minor Arabic terminal revisions after the rejected B2 attempt. Prepare a blinded, balanced qualitative first-read pilot for original Latin R2-B vs R2-C and Arabic typographic A vs B, and inspect actual merchant interface constraints before claiming production fit.

## Sources and verified product evidence
- Brand Master Instructions V2; ChatGPT Project Entrypoint; PROJECT_STATUS; PROJECT_INDEX; DECISION_LOG.
- 11-validation/R2_WORDMARK_MERCHANT_PERCEPTION_TEST_PROTOCOL_V0.1.md.
- 08-visual-identity/logo/R2_B_CUSTOM_WORDMARK_V0.2.svg and R2_C_ANGULAR_CUSTOM_WORDMARK_V0.3.svg.
- 08-visual-identity/typography/ARABIC_WORDMARK_A_B_TREATMENT_ASSESSMENT_V0.1.md.
- Live read-only GitHub check of jetshop7/wossol-platform main: apps/frontend/src/app/merchant/MerchantShell.tsx, and docs/ui/merchant/MERCHANT_HOME_SHELL_UI_SPEC.md. MerchantShell contains merchant-brand, collapsible left sidebar, workspace/store state; specification makes workspace/store context prominent and defers full Arabic RTL refinement. This is actual repository evidence, not a running browser test.

## Produced artifacts (conversation ZIP)
WOSSOL_MERCHANT_FIRST_READ_PILOT_V01.zip contains:
- Four separate 960x360 black-on-white participant-facing PNG stimuli, blind codes M01-M04. Ink heights normalized. M01=Latin C, M02=Latin B, M03=Arabic humanist B, M04=Arabic geometric A. The code key must be kept from participants.
- MODERATOR_OVERVIEW.png (facilitator only).
- BALANCED_ORDERS.csv (10 alternating participant orders).
- MERCHANT_RESPONSES_EMPTY.csv (empty anonymized response sheet, no fabricated responses).
- FACILITATOR_GUIDE_AR.md (Arabic 3-second first-read and delayed-recall interview script).
- SHELL_PLACEMENT_SCHEMATIC.png (real code/spec-informed placement schematic, NOT a production screenshot).
- Original archived Latin paths as source SVG and README.

## Scope and test discipline
Target 6-10 consenting actual merchants for a directional qualitative pilot, balanced by Arabic/Latin familiarity and business stage. Show single stimuli separately for 3 seconds; collect unprompted exact reading, company category guess, descriptors, readability/trust/distinctiveness reasons, delayed spelling/recall, and volunteered similarities. Counterbalance order; never show moderator overview first. No quantitative significance claim from small pilot.

## Evidence / interpretation
Zero participant responses so far. No empirical winner, recognition rate, merchant preference or legal clearance established. B2 was rejected in previous local desk study; exclude it from the pilot. Arabic A/B are typeface-based specimens, not original proprietary Arabic wordmarks. The code confirms structural shell constraints, not pixel-perfect logo fit or RTL deployment.

## Recommendation
TEST: run merchant pilot before additional lettering rounds. If C fails first-read, reconsider Latin direction; if B reads but feels generic, do not auto-approve. Do not use the schematic as an app screenshot. A small number of merchants is directional evidence only.

## Decisions
No new user approval, no final logo, icon, typography or color decision.

## Next actions
Collect real consented anonymized merchant responses and analyze errors/reasons; inspect exact merchant-brand markup and CSS for production header dimensions before final lockup. Then decide whether custom Arabic drawing is justified.

## Additional code verification
Actual MerchantShell.tsx uses a temporary W mark, and Wossol text only when sidebar is expanded. globals.css specifies 280px expanded sidebar, 88px collapsed sidebar, and a 36x36px brand-mark box. No approved icon. Local CURRENT_CODE_BRAND_SLOT.png illustrates these code facts; it is not a screenshot.
