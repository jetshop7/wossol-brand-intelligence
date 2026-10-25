# Wossol — Exact 36px Compact Icon & Arabic Shaping QA V0.8
Date: 2026-10-25
Status: UNDER_REVIEW — no approved mark

## Objective
Close the size-accuracy gap in V0.7: test actual bounded 36x36 raster assets for S and W, plus 72x72 and 144x144 exports, and compare in expanded 280px and collapsed 88px sidebar geometry. Verify Arabic wordmark specimen rendering without claiming bespoke Arabic design.

## Source of truth
Read current brand governance V2, entrypoint, README, PROJECT_STATUS, PROJECT_INDEX, DECISION_LOG, V0.6 and V0.7, original R2-C path geometry. Independently fetched the actual merchant brand JSX from `jetshop7/wossol-platform/apps/frontend/src/app/merchant/MerchantShell.tsx` on main: `<span className="merchant-brand-mark">W</span>` with conditional expanded `<strong>Wossol</strong>`. The W is a temporary product placeholder, not brand approval. Sidebar widths and brand mark sizes follow previously verified shell evidence, while this visual mockup is a **code-informed simulation**, not a live screenshot or deployed change.

## Artifacts executed
- `ICON_S_36_dark.svg`, `ICON_S_36_light.svg`, `ICON_W_36_dark.svg`, `ICON_W_36_light.svg` — precise 36x36 square SVG viewBoxes and exports.
- PNG assets at 36, 72 and 144 actual pixels for each glyph/surface combination.
- `V08_36PX_SHELL_AND_BILINGUAL_BOARD.png` — 1500x1030 comparison board, with embedded exact 36px icons and 4x nearest-neighbor pixel diagnostics.
- `LATIN_C_UNCHANGED.svg` — original C geometry copied without modification.
- `ARABIC_FONT_REFERENCE_LIVE.svg` — font-based live-text reference, not a custom Arabic vector logo. Board Arabic rendered via proper bidirectional shaping with Pillow/RAQM after Inkscape's initial Arabic alignment/clipping proved unreliable.
Delivered ZIP: `WOSSOL_V08_36PX_AND_BILINGUAL_GATE.zip` in conversation. No font files included.

## Findings and quality gate
1. Both glyphs fit the actual 36x36 square with 26px maximum internal glyph extent and 5px minimum intended margin. Pixel exports have actual 36, 72 and 144 dimensions. They remain crisp enough to distinguish from background in desk inspection.
2. S retains C wordmark DNA but can be read as numeral 5. Its cropped negative space is more fragile at 36px than a plain W. W is more obviously linked to the initial but generic, weak in differentiation.
3. Neither is proven recognizable to merchants or legally distinctive. **HOLD S** for possible icon-only challenger; **REJECT W as final distinctive solution**. Do not replace temporary product W.
4. Arabic specimen now visually renders the full correct word `وصول`, but is standard Noto Sans Arabic. A custom Arabic wordmark **has not been designed**. Merely converting font glyphs to outlines would not make it custom design.
5. Production integration, live browser screenshot, actual merchant research, legal clearance and custom Arabic outlines were not executed. No colors, typography or final logo approved.

## Critical next step
Stop repeating S-vs-W comparisons. Reframe compact icon requirement as a full logo-family challenge: either derive a distinctive reduced signature from an approved bilingual wordmark family or commission an independent original symbol concept only after a strategic and competitive visual differentiation brief. Build genuinely customized Arabic outlines with proper joining, dots and optical harmony; compare beside C at same perceived visual weight. No merchant pilot gate per D-014.

## Decision status
Assistant recommendation only, no user approval.
