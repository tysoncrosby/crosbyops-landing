# Design: App mockups on the Crosby Ops home page

**Date:** 2026-06-30
**Status:** Approved (design), pending implementation plan
**Scope:** `index.html` + `styles.css` in `crosbyops-landing`. No JS, no build step, no new assets.

## Goal

Show prospective operators what Crosby Ops actually looks like, directly on the
home page. Replace abstract module copy alone with faithful, lightweight
recreations of the real app screens (sourced from the `kicco-ops` app repo),
built as HTML/CSS so they stay sharp, themeable, and easy to edit.

## Approach

Convert the existing four-card "What it does" grid (`#what` / `.modules`) into
**four alternating feature rows**. Each row keeps the module's icon, title, and
existing body copy on one side, and adds an app-window mockup on the other.
Image side alternates left/right per row.

This reuses existing copy (DRY) and integrates visuals into the story the page
already tells, rather than adding a separate gallery section.

## Components

### 1. Feature row (`.feature-row`)
- Two-column layout: `.feature-copy` and `.feature-mock`.
- Default: copy left, mock right. Every second row (`.feature-row.reverse`)
  swaps order via CSS (e.g. `direction` or explicit `order`).
- Vertical rhythm matches existing section spacing.

`.feature-copy` reuses the existing module markup: `.module-icon` (the existing
SVG), `.module-title`, `.module-body`. Optionally a short 2–3 item `.feature-points`
list of specifics already implied by the copy (no new claims).

### 2. App-window frame (`.app-window`)
Reusable wrapper giving each mockup a "this is the real product" signal.
- `.app-window-bar` — slim title bar: small Crosby mark (reuse hero SVG glyph or
  three traffic-light dots in brand tints) + screen label
  (e.g. "Rostering · Week of 30 Jun").
- `.app-window-body` — holds the mockup. White surface, internal padding.
- Border `--line-strong`, radius ~14px, `--shadow-md`. Calm, matches site.

### 3. The four mockups (pure HTML/CSS, no images)

Built from the real screens in `kicco-ops/src`. Use real labels (verified):

- **Rostering** (`RosterGrid` + `ShiftCostCard`/`CostBreakdown`):
  mini grid of ~3 staff rows × 5 weekday columns, a few colored shift chips
  (orange/gold/navy tints), and the signature **wage-cost stat**:
  "Wage cost 28.4% · on budget" in green (`--green`). "Recommended" hint allowed.

- **Timesheets** (`Timesheets.js`):
  week list of staff with hours and status pills — **Approved** (green) /
  **Pending** (warm-grey/gold). A primary **Export to Xero** button (uses the
  real "to Xero" wording).

- **Purchasing** (`InvoiceReconcile.js` / `PlaceOrder`):
  invoice reconcile card — supplier invoice matched to an order, 2–3 line items,
  **Subtotal / GST / Total** rows, and a **Matched ✓ · Post to Xero** state.

- **Leave** (`Leave.js`):
  leave requests list — staff, leave type (Annual / Personal), date range,
  status pills (Approved / Pending / Declined), a **Balance** figure, and
  Approve/Decline affordances.
  - The existing fourth module card is renamed from "Leave & HR" to **"Leave"**,
    and its body copy is rewritten to drop the HR / onboarding-wizard language.
    Nothing on the page should say "HR" or reference onboarding. Suggested copy:
    "Staff request leave, you approve or decline, balances update automatically.
    Requests, balances and approvals in one place."

Mockups show realistic-but-fictional venue/staff data (no real customer data).

## Styling

- Add tokens to `:root` in `styles.css`:
  - `--green: #4A8A5E;` (on-budget / approved — from real app)
  - `--red: #C0392B;` (over / alert — from real app)
- Reuse existing `--line`, `--line-strong`, `--shadow-sm/md`, radii, palette.
- Status pills: small rounded chips, tinted background + matching text color.

## Responsive

- `@media (max-width: 760px)`: feature rows stack to one column; copy first,
  mockup second; `.feature-row.reverse` order override is neutralized so order
  is consistent on mobile. Mockups full-width.
- Mockups must scale down without horizontal overflow — fluid widths / wrapping,
  no fixed pixel widths wider than the column. Tiny text remains legible.

## Out of scope (YAGNI)

- No carousel / slider / JS interactivity.
- No exported PNG/image assets.
- No changes to apply / privacy / support pages.
- No new product claims — copy stays as-is or is lightly factored.
- No "HR" or onboarding messaging anywhere on the page (per decision).

## Verification

Run the static site locally (e.g. `python3 -m http.server`) and view `index.html`
in a browser at desktop (~1200px) and mobile (~375px) widths. Confirm:
1. Four alternating rows render, image side alternates, all four mockups present.
2. No horizontal overflow at mobile width; rows stack copy-then-mockup.
3. Colors/shadows read as native to the site; green/red accents correct.
4. Existing nav, hero, built-for, contact, footer unchanged.
