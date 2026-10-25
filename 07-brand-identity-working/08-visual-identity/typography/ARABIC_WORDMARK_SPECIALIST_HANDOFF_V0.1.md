# WOSSOL — Arabic Wordmark Specialist Handoff

**Date:** 2026-10-25 · **Status:** HANDOFF REQUIRED · **Version:** 0.1  
**Scope:** Focused Arabic lettering construction for وصول and optical pairing with experimental Latin R3. No new identity system, colors, slogans or applications.

## Decision

**No viable bespoke Arabic wordmark candidate is delivered by this round. Specialist Arabic lettering construction is required.** I did not relabel the prior rejected V0.9/Round-2 sketches or an installed font as custom work. The available environment can render correctly shaped Arabic for visual reference but does not provide a supported professional font-outline/shaping workflow with safe, deliberate editing of joined glyph contours. The installed Arabic Typesetting and Segoe UI specimens are system-font references, not production assets; no font files are embedded or redistributed.

The [reference comparison board](ARABIC_WORDMARK_SPECIALIST_REFERENCE_BOARD_V0.1.svg) contains actual shaped Arabic samples in two different typeface styles, beside the experimental R3 Latin paths. Its PNG was rendered and visually inspected at display and reduced specimen sizes. It is a reference comparison—not two custom candidates, native-reader evidence, or an approved identity.

## Anatomy and joining specification

The Arabic text is the Unicode sequence U+0648 U+0635 U+0648 U+0644, read right-to-left. The **logical reading order from right to left** is:

`و` isolated · `صـو` connected (`ص` initial joining form + following `و` final joining form) · `ل` isolated.

In **left-to-right visual layout**, that segment order appears reversed: isolated `ل` · connected `وـص` · isolated rightmost `و`. The visual sequence is not four independently spaced glyphs. The middle join must remain one continuous connection with a clean transition from the `ص` bowl to the `و` final; the outer two joins are intentionally open because `و` cannot join forward to the next letter in the word. The word has no dots or required vowel diacritics.

This distinction follows the Arabic right-to-left contextual-joining model in [W3C Arabic & Persian Layout Requirements](https://www.w3.org/TR/alreq/) and [Microsoft's Arabic OpenType guidance](https://learn.microsoft.com/en-gb/typography/script-development/arabic). The W3C notes that forms depend on neighboring letters and that joined overlap/space cannot be treated as ordinary Latin letter spacing. Microsoft describes contextual form selection (isolated, initial, medial, final) before connection and positioning.

Construction points for the specialist:

- Preserve the reference word's RTL baseline, the two deliberate disjoining gaps, and the single central `صـو` join; inspect at actual reading size, not only as an enlarged drawing.
- Keep the lam's ascender distinctive but not so tall that it pulls the word away from the R3 visual center. Avoid an isolated upright stroke that looks detached from the baseline.
- Make the ṣād bowl/counter unambiguously ṣād-like. Its bowl, shoulder and connection are the most important custom contour; do not make it an abstract wave or collapse it into the waw.
- Give each waw a recognizable loop/shoulder and tail with enough counter at small sizes. The isolated final/rightmost waw and the joined final-form waw must be designed contextually, not duplicated mechanically.
- Preserve optical white space around the two non-joining boundaries. Do not compensate by stretching a joined connection or inserting artificial gaps inside it.
- Use smooth Bézier continuity through the central join, consistent stroke rhythm, controlled terminals, and optically balanced—not mechanically equal—spacing across the three joining segments.

## Two genuinely different lettering briefs

These are **design routes for a specialist**, not artwork or pre-approved concepts:

1. **Naskh-informed humanist custom:** use a properly licensed professional Naskh specimen only as an anatomical reference. Retain calligraphic contrast and open counters, but simplify entry/exit strokes and tune the lam's ascender and paired wāw shapes for a compact contemporary wordmark. Avoid simply outlining or uniformly compressing the reference font.
2. **Soft Arabic sans / restrained Kufi custom:** construct a lower-contrast, broad-bowl drawing with controlled near-horizontal rhythm and softened corners that can sit beside R3's rounded Latin strokes. Keep recognizably Arabic proportions and the contextual `صـو` join; do not impose Latin modular geometry, chamfers, or an artificial grid.

Develop both from the same correct shaped reference and the same width/height brief. Record the specific changed contours (lam, ṣād initial, waw final, isolated waw), not merely a global scale or weight change. Reject either route if its distinctiveness comes at the cost of reading.

## R3 pairing and optical gate

The comparison specimen uses the custom R3 path geometry reproduced in the [Round-2 bilingual SVG source](ROUND2_FINAL_WORDMARK_AND_LOCKUP_BOARD_V0.1.svg); R3 is still experimental and is not itself approved. The specimens show that the Naskh reference carries softer contrast than R3, while the Segoe UI Arabic reference is closer in apparent weight but more generic. This is only a desk comparison; neither determines the final route.

For the specialist's side-by-side lockup: begin with equal apparent cap/x-height zones, then optically correct for Arabic ascender height, bowl density and word width. Compare Arabic above/below R3 and in horizontal RTL-aware pairing. Do not match by font-size number or bounding-box height alone. At small sizes confirm that R3 counters and Arabic counters remain open and that the Arabic middle connection remains continuous.

Required proof before a route can advance:

- Editable vector path source for each candidate, with deliberate construction notes and no embedded/redistributed font file.
- Correctly shaped reference board, large specimen, and exact 72, 48, 36 and 24 px exports, plus 160/240/400 px lockup previews.
- Pixel inspection at native size and after rasterization; check counters, joins, tails, baseline, optical center and anti-aliasing.
- Blind word identification/readability with multiple native Arabic readers from relevant regions. Record actual participants and responses; do not infer reader approval from designer inspection.
- Commercial font/derivative rights and trademark clearance reviewed separately; neither visual quality nor open font licensing clears the Wossol mark.

## Sources and inspected artwork

- [Round 2 creative refinement report](../ROUND2_CREATIVE_REFINEMENT_REPORT_AR_V0.1.md): previous Arabic path trials rejected for anatomy/optical quality; R3 is a Latin craft base only.
- [Round-2 Arabic reference board source](ARABIC_REFERENCE_VS_CUSTOM_LETTERING_ROUND2_V0.1.svg) and [Round-2 bilingual wordmark/lockup source](ROUND2_FINAL_WORDMARK_AND_LOCKUP_BOARD_V0.1.svg): inspected; both show earlier custom Arabic sketches rejected and R3 experimental paths.
- [R2-C source](../logo/R2_C_ANGULAR_CUSTOM_WORDMARK_V0.3.svg) and [R3 transform-unit symbol source](../logo/R3_TRANSFORM_UNIT_PROTOTYPE_V0.1.svg): inspected. The latter is a compact symbol prototype, not the R3 Latin wordmark.
- [W3C Arabic & Persian Layout Requirements](https://www.w3.org/TR/alreq/) and [Microsoft Developing OpenType Fonts for Arabic Script](https://learn.microsoft.com/en-gb/typography/script-development/arabic): contextual forms, joining, positioning, and reference-typeface notes.
- [Arabic Typesetting family information](https://learn.microsoft.com/en-us/typography/font-list/arabic-typesetting) and [Segoe UI family information](https://learn.microsoft.com/en-us/typography/font-list/segoe-ui): these identify the specimens used on the board; neither font is claimed as original lettering or included as a font asset.

## Disposition

**REJECT / HANDOFF.** The prior custom Arabic tests remain rejected. Do not publish a logo or call the current SVG specimens “custom Arabic wordmarks.” The next viable production step is a qualified Arabic type designer executing the two routes above, followed by independent native-reader and owner review. This document and its reference board are the precise handoff; they do not make a final design decision.
