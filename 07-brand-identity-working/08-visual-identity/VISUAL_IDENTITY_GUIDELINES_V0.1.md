# Wossol Visual Identity Guidelines V0.1

**Status:** UNDER_REVIEW — a design proposal, not an approved identity.
**Scope:** Latin-first visual system proposal. No logo, icon, palette, type system, or application copy is approved for public deployment.
**Related assets:** [Complete visual identity system SVG](WOSSOL_COMPLETE_VISUAL_IDENTITY_SYSTEM_V0.1.svg) · [Rendered PNG board](WOSSOL_COMPLETE_VISUAL_IDENTITY_SYSTEM_V0.1.png) · [Arabic executive report](VISUAL_IDENTITY_EXECUTIVE_REPORT_AR_V0.1.md)

## Strategic and evidence boundary

This is a professional desk proposal based on the current brand-intelligence record, not merchant preference research. Merchant discovery has not yet validated the design or the proposed copy. The strongest repeated product pattern is selected commercial context kept connected, bounded merchant agency, and clearer operational accountability. Claims of universal coverage, a complete closed loop, causal attribution, quantified time savings, guaranteed growth, prediction, or broad global availability are not established. Product mockups are synthetic examples only; none depicts live customer or merchant data or promises feature parity with the current application.

## Creative principle: The Evidence Thread / Open Record

Use a restrained, mostly linear composition to keep related commercial context near an action and its available evidence. It is a layout grammar, not a logo, network map, lifecycle guarantee, or claim that every data relationship exists. Draw a solid thread only across verified/available relationships; use a visible break, dashed segment, or unresolved marker wherever a relationship is absent, conditional, or unverified. Lime nodes can mark a checkpoint or attention point, not success by themselves. Leave continuation open without suggesting universal product coverage or a guaranteed economic outcome.

Use open panels, editorial whitespace, thin separators, and one primary rail at a time. Avoid decorative mesh, global maps, growth curves, generic SaaS blobs, arbitrary arrows, or finance-led language. A straight line is not a substitute for evidence.

## Color proposal

| Token | Value | Proposed role | Contrast observations |
|---|---|---|---|
| Harbor Ink | `#153B3A` | Primary wordmark, key headings, primary controls | 11.29:1 on Warm Paper; 9.64:1 on Signal Lime |
| Night Field | `#0E2528` | Dark fields, inverse surfaces | 14.76:1 with Warm Paper; 12.60:1 with Signal Lime |
| Signal Lime | `#D7F06A` | Sparse checkpoint, focus accent, selected highlight | Use Harbor Ink or Night Field text; avoid as a large text fill |
| Warm Paper | `#F7F6F0` | Main canvas / light surface | 11.29:1 with Harbor Ink; 4.72:1 with Slate |
| Mist | `#E7EEEB` | Secondary panels and quiet fills | Pair with dark text |
| Body Ink | `#21302E` | Paragraph text | 4.66:1 on Signal Coral |
| Slate | `#62716F` | Secondary text on Warm Paper | 4.72:1; do not reduce contrast by opacity |
| Signal Coral | `#E07A5F` | Caution/temporary marker, never sole status cue | Body Ink gives 4.66:1; avoid white small text |
| Blue Note | `#587A8C` | Non-text/large-text accent only on Warm Paper | 4.24:1 on Warm Paper: fails the 4.5:1 normal-text benchmark |

Ratios are calculated for the listed opaque sRGB token pairs; they are not a page-wide accessibility audit. W3C WCAG 2.2 AA requires 4.5:1 for normal text and 3:1 for large text; verify actual rendered states, focus indicators, disabled states, imagery, gradients, and user-selected themes before implementation. [W3C contrast minimum](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html). Never use color alone to communicate status. Reserve Lime for a few meaningful accents rather than coloring every control.

## Typography proposal

- Latin: Inter Variable, with regular 400, medium 500, semibold 600, bold 700.
- Arabic supporting text: Noto Sans Arabic Variable, with appropriate Arabic shaping and line height; do not mirror Latin forms or use the Latin wordmark as an Arabic lockup.
- Suggested scale: display 48/56, H1 32/40, H2 24/32, body 16/24, compact UI 14/20, labels 12/16. Arabic body copy should begin around 16/28 and be tested with real localized content.
- Both type families are published under SIL Open Font License 1.1 in their official repositories: [Inter OFL](https://github.com/google/fonts/blob/main/ofl/inter/OFL.txt), [Noto Sans Arabic OFL](https://github.com/google/fonts/blob/main/ofl/notosansarabic/OFL.txt), and the family source's [Noto Arabic license](https://github.com/notofonts/arabic/blob/main/OFL.txt). This is a source-level license check, not legal advice or a deployment review. Preserve required notices when redistributing font files and confirm the exact packaged font/version and embedding method before release.
- Neither family was installed in the local rendering environment during this proposal. The SVG names the intended families, but the PNG board uses the available system fallback; it is not an exact specimen of Inter or Noto Sans Arabic. Obtain fonts and rerender before final typography approval. Arabic shaping and visual quality have not received native-reader validation; custom Arabic lettering remains deferred, not rejected or approved.

## Wordmark and compact mark

The provisional primary is A / Clear Stroke from the prior Latin wordmark review. Use the editable path artwork as supplied, in one color: Harbor Ink on Warm Paper or Paper on Night Field. Keep at least 0.25× cap-height clearspace and use a provisional digital minimum width of 112px pending real-device review. Do not add shadows, outlines, gradients, or the Lime color as the default wordmark fill. The mark is under review and may still be revised or rejected by the owner.

No distinctive compact mark has cleared review. A W avatar is shown only as a temporary prototype fallback and must not ship as a final icon, favicon, app tile, or social avatar. Prefer a wordmark crop only where the full readable word remains; otherwise use a neutral product placeholder until the owner authorizes a symbol direction.

## Layout, product UI, and imagery

- Marketing pages: use a flexible 12-column editorial grid, generous margins, and one dominant message. The board's sample navigation and copy are illustrative and require product/legal review.
- Product interface: use a 4px base grid and common 8/16/24/32/48 spacing steps, clear hierarchy, restrained dividers, and accessible focus states. Keep brand accents away from semantic success/error states.
- Imagery: if later needed, prefer permissioned, contextually true merchant environments and genuine product captures. Avoid generic handshakes, anonymous stock portraits, trucks, map routes, abstract “AI” effects, or imagery implying capabilities/markets not evidenced.
- Icons: use a coherent 24px vector grid and consistent ~2px stroke with rounded terminals. Use an arrow only when it encodes a real navigation/action direction; never let an icon alone carry a status or capability claim.
- Examples shown in the board: landing page, dashboard, mobile concept, social identity/feed tile, paid-social concept, and company profile. All interface data and copy are synthetic design placeholders. The mobile screen does not assert parity with a shipped mobile product.

## Arabic localization

Use Noto Sans Arabic as a proposed supporting text family after exact-font rendering and native-language proofreading. Build RTL layout semantically: right-aligned reading order, bidirectional handling for numerals/Latin tokens, locale-appropriate dates and punctuation. Do not simply mirror the Latin logo or invent custom Arabic letterforms. Custom Arabic wordmark development is deferred, not rejected or approved.

## Open risks and owner decisions

1. The provisional A wordmark is clear but familiar-looking; no proprietary compact signature exists.
2. The Evidence Thread may feel diagrammatic if used on every surface; the system needs restraint and real-product trials.
3. The exact selected fonts have not been locally rendered; system fallback affects the specimen, metrics, and Arabic shaping.
4. No native Arabic reader, merchant audience, or accessibility specialist has validated the board. No browser/assistive-technology contrast or keyboard audit has been done.
5. The product, dashboard, mobile, campaign, and profile applications are proposals, not verified production states or approved public claims.
6. Trademark clearance for Wossol and final artwork/legal review remain unresolved.

Owner authorization is required before adopting the provisional wordmark, palette, typography, graphic principle, application copy, and whether a compact identity is required. Arabic custom lettering remains deferred. Nothing in this document constitutes final identity approval.
