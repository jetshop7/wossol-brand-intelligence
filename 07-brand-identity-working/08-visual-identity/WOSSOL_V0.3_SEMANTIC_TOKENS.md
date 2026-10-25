# WOSSOL Visual Identity V0.3 — Semantic Theme Tokens

**Date:** 2026-10-25
**Status:** UNDER_REVIEW — proposal only; no owner approval
**Version:** 0.3
**Scope:** Light and Dark product/brand tokens, interaction states, data visualization and implementation notes. Companion boards: [dual-theme system](WOSSOL_V0.3_DUAL_THEME_SYSTEM.svg), [product applications](WOSSOL_V0.3_PRODUCT_UI_APPLICATIONS.svg), [palette comparison](WOSSOL_V0.3_PALETTE_COMPARISON.svg).

## Principles

- Light and Dark are peers; mode selection is user-controlled and must respect system preference without removing an explicit user choice.
- A role maps to a mode-specific value. Do not implement Dark by mechanically inverting Light.
- Signal color never carries state alone. Pair it with a label, icon/shape and, where useful, text or border.
- The primary palette retains Harbor Ink `#153B3A`, Night Field `#0E2528`, Signal Lime `#D7F06A` and Warm Paper `#F7F6F0`. The alternative Atlas / Apricot palette is a controlled challenger only.
- Proposed fonts remain Inter and Noto Sans Arabic; Arabic custom lettering is deferred, not rejected or approved. Typeface choice/version and RTL/product rendering are not approved by this token proposal.

## Primary semantic tokens

| Semantic role | Light | Dark | Use / notes |
|---|---:|---:|---|
| `canvas.app` | `#F7F6F0` | `#09191C` | Main application background |
| `surface.raised` | `#FFFFFF` | `#152F33` | Cards, dialogs, menus |
| `surface.sunken` | `#F0F3EE` | `#0B2023` | Inputs, table header, secondary panes |
| `surface.selected` | `#E7EEEB` | `#203B3E` | Selected/active navigation and rows; add a 3px rail |
| `surface.hover` | `#F0F3EE` | `#1B373A` | Hover/focused-adjacent surface, not the focus ring |
| `sidebar.background` | `#153B3A` | `#0B2023` | Navigation remains dark in both modes for continuity |
| `sidebar.selected` | `#315957` | `#29494A` | Selected nav; include text and selected marker |
| `text.primary` | `#142925` | `#F7F6F0` | Body and headings |
| `text.secondary` | `#596D68` | `#AEC0BB` | Supporting text; not for disabled labels |
| `text.muted` | `#62716F` | `#91A5A0` | Quiet metadata; verify at actual sizes |
| `text.on-action` | `#F7F6F0` | `#142925` | Text on Harbor primary / Lime primary respectively |
| `border.default` | `#CFD8D2` | `#294448` | Panels, table dividers, form outlines |
| `border.strong` | `#9EADA6` | `#59716E` | High-emphasis boundaries and chart axes |
| `action.primary` | `#153B3A` | `#D7F06A` | Primary action; reverse foreground in each mode |
| `action.secondary` | `#E7EEEB` | `#152F33` | Secondary action with visible border |
| `accent.checkpoint` | `#D7F06A` | `#D7F06A` | Sparse focus/checkpoint/highlight; dark text |
| `focus.ring` | `#153B3A` | `#D7F06A` | 3px exterior ring plus 2px offset; never remove |
| `status.success.text` | `#256444` | `#B9F1C8` | Pair with check icon and “Success” label |
| `status.success.fill` | `#E5F3E9` | `#24483B` | Low-area status surface |
| `status.warning.text` | `#795000` | `#FFDCA4` | Pair with triangle icon and “Review” label |
| `status.warning.fill` | `#FFF0D6` | `#4B3224` | Low-area status surface |
| `status.error.text` | `#A93F32` | `#FF9B83` | Pair with error icon and explicit wording |
| `status.error.fill` | `#FFF0E9` | `#462B27` | Low-area status surface |
| `status.info.text` | `#356D8A` | `#88B8D2` | Pair with info icon and explicit wording |
| `status.info.fill` | `#E8F1F5` | `#1E3D4B` | Low-area status surface |
| `state.disabled` | `#E5E9E5` / `#75827E` | `#203134` / `#91A5A0` | Fill / text; keep readable though WCAG exempts inactive controls |

### Interaction states

| Component | Rest | Hover | Active / pressed | Selected / checked | Focus | Disabled |
|---|---|---|---|---|---|---|
| Primary button | role `action.primary` | darken 6% (Light), lighten 6% (Dark); keep text contrast | darken 10% / lighten 10%; 1px inset cue | not applicable; toggle action uses selected row treatment | `focus.ring` 3px + 2px offset | `state.disabled`; no pointer, retain label |
| Secondary button | `surface.raised` + `border.default` | `surface.hover` | `surface.selected` | selected fill + checkmark where toggle | same ring | disabled tokens |
| Navigation | `sidebar.background` | mode-specific `sidebar.selected` at 55% only if label contrast holds | 1px inner edge | selected fill + 3px leading rail + weight change | same ring, not color-only | keep visible with explicit “Unavailable” only when appropriate |
| Input / dropdown | `surface.raised`, 1px border | `border.strong` | active caret / text cursor | selected option has fill + check | same ring | stable read-only value; distinguish disabled from empty |
| Table row | transparent / surface | `surface.hover` | `surface.selected` | 3px rail + label or check | keyboard outline | retain legible values; actions disabled individually |
| Dialog | `surface.raised`, 24px max radius | not applicable | backdrop only | not applicable | focus trap / return focus | not applicable |

### Component and chart rules

- **Tables/data grids:** raised surface, quiet header on sunken surface, 48px minimum row target, visible 1px separators, pinned labels for statuses; no dense color blocks.
- **Cards/dialogs:** 12–16px radius in UI; no persistent shadow in Dark. In Light, a restrained elevation shadow is allowed only for transient overlays: `0 8px 24px rgba(14,37,40,.12)`. Dialog backdrop `rgba(9,25,28,.48)`.
- **Charts:** assign direct labels and units; measured series use solid strokes, estimates/projections use dashed strokes and a textual label. Never show hypothetical causal links as solid. Proposed sequence: Harbor `#153B3A`, blue `#356D8A`, success `#256444`, warning `#A46A00`, coral `#A93F32`; Dark equivalents: Lime `#D7F06A`, `#88B8D2`, `#B9F1C8`, `#FFDCA4`, `#FF9B83`. Use line style/marker in addition to hue. Keep gridlines low contrast, not invisible.
- **Focus/keyboard:** 3px outline and 2px offset; preserve visible focus in both modes and on selected controls. Do not clip outlines inside cards.
- **Reduced motion:** transition color/border only (120–180ms); no animated data, progress or value change implied by a decorative animation.
- **Elevation:** no blur shadows on core cards; rely on border and tonal layering. Shadows are not a substitute for contrast.

## Contrast spot-check (opaque sRGB, calculated)

| Pair | Ratio | Result |
|---|---:|---|
| Light primary text / app canvas | 14.12:1 | Passes 4.5:1 normal-text benchmark |
| Light secondary / app canvas | 5.09:1 | Passes |
| Harbor / Warm Paper | 11.29:1 | Passes |
| Harbor / Lime | 9.64:1 | Passes |
| Dark primary text / app canvas | 16.61:1 | Passes |
| Dark secondary / app canvas | 9.47:1 | Passes |
| Dark primary text / raised surface | 13.05:1 | Passes |
| Dark secondary / raised surface | 7.44:1 | Passes |
| Light error / Warm Paper | 5.63:1 | Passes |
| Dark error / app canvas | 8.80:1 | Passes |
| Light info / Warm Paper | 5.23:1 | Passes |
| Dark info / app canvas | 8.42:1 | Passes |
| Light dark-text button / Lime | 12.06:1 | Passes |
| Dark Harbor / Lime | 12.60:1 | Passes |

These are selected flat-color pairs, not a complete WCAG audit. They do not validate focus visibility on every state, disabled semantics, gradients, borders as non-text contrast, color-vision differences, browser screenshots, mobile/zoom, Arabic shaping, or actual platform CSS. Test all states and data visualizations in the implementation before release.

## Alternative palette — Atlas / Apricot

One controlled challenger only: Light canvas `#F7F6F0`, surface `#FFFFFF`, sidebar/action `#193A66`, dark field `#101D2D`, structural fill `#EEF2F7`, accent `#F2B66D`; Dark canvas `#101D2D`, raised `#1B2C42`, sidebar `#0C1726`, selected `#263E5D`, accent `#FFD08A`. Use a low-chroma blue note `#4A789A` for charts; retain common semantic status colors. This is not a full theme configuration or approved replacement. The exact same Home and campaign samples are compared in [the palette board](WOSSOL_V0.3_PALETTE_COMPARISON.svg).

## Implementation and evidence boundary

Current merchant navigation and examples derive from the V0.2 correction report's verified `wossol-platform` reference (`dev/wossol-integration` at `3ed1386fa88496a2e80b5e144b14a198f6e1bc63`). This package does not re-audit the product and makes no product-code changes. Quantities and named demo operator in the artwork are synthetic. No public site or campaign is claimed to exist. No outcome, customer testimonial, causal advertising lift, or universal capability claim is proposed as fact.

**Gate:** UNDER_REVIEW. Confirm mode preference behavior, implementation tokens, color-vision checks, platform runtime/device QA, localized Arabic rendering and owner preference before adopting these tokens.
