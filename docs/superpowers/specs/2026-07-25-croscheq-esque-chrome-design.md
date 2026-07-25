# Design: CrosCheq-esque page chrome for crosbyops.com

**Date:** 2026-07-25
**Status:** Approved (design), pending implementation plan
**Scope:** `styles.css` + `index.html` in `crosbyops-landing`. Structural CSS/HTML
only. No copy changes, no new pages, no build step, no new dependencies.

## Goal

Bring crosbyops.com's page *chrome* (backgrounds, section rhythm, footer)
closer to the visual language documented in the CrosCheq brand guide, without
using the CrosCheq name, wordmark, or handle anywhere on the site. Content,
copy, and information architecture are unchanged — this is a visual-only pass.

Explicitly **not** in scope for this round: the CrosCheq wordmark (held back
per standing direction until trademark clears), a tick-burst/confetti motif
(that's specific to the social/coming-soon assets, not the core brand guide,
and was ruled out during review), and corner glow blobs on cards or sections
(tried in mockup, explicitly declined by the user — "leave out blobs for
now").

## Background: what the brand guide actually specifies

Reviewed `croscheq-brand/brand-guide/croscheq-brand-guide.html` directly
(not the coming-soon marketing page, which uses a different, more decorative
treatment specific to social assets). Findings relevant to this design:

- **Palette and typography are already identical.** The guide states colour
  is "carried over from Crosby Ops without change," and body/heading type
  stays General Sans (Space Grotesk is wordmark-only). No changes needed.
- **Section rhythm alternates.** The guide's `.section` / `.section.alt`
  pattern alternates an off-white background and a warm surface tint
  (`#F4F3EC`) roughly every section, with one dark navy section used for
  emphasis. crosbyops.com today is flat off-white for nearly the whole page,
  with only one alternate band ("Built for").
- **The footer is a dark navy moment.** The guide's footer is a navy gradient
  card with the mark and wordmark. crosbyops.com's footer today is flat white
  with plain text, no mark.
- **Corner glow blobs** (soft, heavily blurred radial-gradient circles in
  orange/gold) are a recurring device across the guide's cover, section
  headers, cards, and footer — but this was tried in a live-scale mockup and
  the user opted out of it for this round. Not part of this design.

## Design

### 1. Section background rhythm

Give `.more` ("And everything else") and `.integrations` ("Works with the
tools you already use") a proper full-bleed section treatment instead of
their current constrained centered block:

- `.more`: full-bleed background `var(--surface-2)` (`#F4F3EC`), `padding:
  80px 0`. Existing inner content (`.section-head`, `.more-grid`) moves inside
  a new `.section-inner` wrapper (`max-width: 1200px; margin: 0 auto; padding:
  0 32px`) so the content width is unchanged, only the background now bleeds
  full width.
- `.integrations`: same full-bleed restructure, but stays `var(--off-white)`
  (i.e. visually unchanged from today) so the alternation reads as
  off-white → cream ("More") → off-white ("Integrations") → cream ("Built
  for", unchanged) → off-white ("Contact", unchanged).
- `.built-for` and `.contact` are unchanged — `.built-for` already uses
  `--surface-2`, `.contact` already renders on the body's off-white.

Net effect: the alternating rhythm the guide uses becomes visible down the
page, using only the two background tints crosbyops.com already has as
tokens (`--off-white`, `--surface-2`). No new colours.

### 2. Dark footer with the mark

Replace the flat white footer with the guide's dark navy treatment:

- `.footer` background becomes `linear-gradient(135deg, var(--navy) 0%,
  var(--navy-2) 100%)` (the same gradient already used for the hero card),
  text colour white.
- `.footer-brand` gains the existing Crosby Ops tick-mark SVG (already used
  in the nav brand lockup — same two-stroke navy-orange-gold path, reused
  as-is) at 18px, inline before the "Crosby Ops" text, gap 8px.
- `.footer-meta` link colour becomes `rgba(255,255,255,0.55)`, hover state
  `var(--white)`.
- Layout is **not** restructured to match the guide's fully centered/stacked
  footer — the existing horizontal flex layout (brand left, links right) is
  kept as-is, just recoloured. This was a deliberate scope call to test one
  variable (dark vs light) without also changing the layout shape in the
  same pass.

### 3. Hero — unchanged

The hero already uses the navy gradient card + radial orange glow that
matches the guide's dark-section treatment. No changes.

## Verification

Prototype built and reviewed at real scale (1280px viewport) in a scratch
copy before this spec was written — confirmed via computed styles
(`.more` background, `.integrations` background, `.footer` background-image,
footer mark presence) and visual review. User confirmed this direction lands
("yes") after an earlier smaller-scale mockup pass didn't ("im not sure this
hits") — the fix was reviewing real page content at full scale rather than
shrunk-down comparison sketches.

## Out of scope / explicitly deferred

- CrosCheq wordmark or name anywhere on crosbyops.com.
- Corner glow blobs (declined).
- Tick-burst/confetti motif (not part of the actual guide; specific to social
  assets).
- Restructuring the footer to a centered/stacked layout.
- Any change to the 6 feature-row mockups, hero dashboard mockup, or nav —
  those already track the app closely per earlier work this session.
