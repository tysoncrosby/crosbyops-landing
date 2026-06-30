# App Mockups on Home Page — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the four-card "What it does" grid on the home page with four alternating feature rows, each pairing module copy with a faithful HTML/CSS recreation of the real Crosby Ops app screen.

**Architecture:** Pure HTML/CSS edits to `index.html` and `styles.css`. No JS, no build step, no image assets. Each mockup is wrapped in a reusable `.app-window` frame and styled with the site's existing design tokens plus two new accent tokens (green/red) lifted from the real app.

**Tech Stack:** Static HTML5, hand-written CSS (CSS custom properties, grid/flexbox). Served as-is via GitHub Pages.

**Note on testing:** This is a static marketing site with no test runner. The "verify" step in each task is a browser visual check. Serve locally with `python3 -m http.server 8000` from the repo root and open `http://localhost:8000/` (hard-refresh after each change).

---

## File Structure

- **Modify** `index.html` — the `<section id="what" class="modules">` block (lines ~53–116) is replaced by a `.features` section containing four `.feature-row` blocks. Section title and the fourth module's title/copy change. Nothing else in the file changes.
- **Modify** `styles.css` — add `--green`/`--red` to `:root`; add a `/* ── Features ── */` block (feature rows, app-window frame, mockup primitives, status pills, mockup-specific classes); add responsive rules to the existing `@media (max-width: 760px)` block. The existing `/* ── Modules ── */` block can stay (its `.module-icon`/`.module-title`/`.module-body` classes are reused inside feature rows); `.module-grid`/`.module-card` become unused but are left in place to avoid churn.

Reused existing classes: `.eyebrow`, `.section-head`, `.section-title`, `.module-icon`, `.module-title`, `.module-body`, and the existing module SVG icons.

---

## Task 1: CSS foundation — tokens, feature rows, app-window frame, pills

**Files:**
- Modify: `styles.css` (`:root` block; new section after `/* ── Modules ── */`)

- [ ] **Step 1: Add accent tokens to `:root`**

In `styles.css`, inside `:root` (after the `--warm-grey:` line, ~line 12), add:

```css
  --green:       #4A8A5E;
  --green-soft:  rgba(74, 138, 94, 0.12);
  --red:         #C0392B;
  --red-soft:    rgba(192, 57, 43, 0.10);
  --gold-soft:   rgba(255, 200, 87, 0.16);
  --orange-tint: rgba(255, 107, 53, 0.12);
  --navy-tint:   rgba(15, 31, 61, 0.06);
```

- [ ] **Step 2: Add the Features layout + app-window + pill styles**

In `styles.css`, immediately after the `/* ── Modules ── */` block (after the `.module-body { ... }` rule, ~line 279), add:

```css
/* ── Features (module + mockup rows) ───────────────────────────── */

.features {
  max-width: 1200px;
  margin: 0 auto 96px;
  padding: 0 32px;
  display: grid;
  gap: 72px;
}

.feature-row {
  display: grid;
  grid-template-columns: 1fr 1.1fr;
  align-items: center;
  gap: 56px;
}
.feature-row.reverse .feature-copy { order: 2; }
.feature-row.reverse .feature-mock { order: 1; }

.feature-copy { max-width: 420px; }
.feature-copy .module-icon { margin-bottom: 18px; }
.feature-copy .module-title { font-size: 26px; margin-bottom: 12px; }
.feature-copy .module-body { font-size: 15px; }

.feature-points {
  list-style: none;
  margin: 18px 0 0;
  padding: 0;
  display: grid;
  gap: 8px;
}
.feature-points li {
  position: relative;
  padding-left: 22px;
  font-size: 14px;
  color: rgba(15, 31, 61, 0.72);
}
.feature-points li::before {
  content: "";
  position: absolute;
  left: 0;
  top: 7px;
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: var(--orange);
}

/* App-window frame */
.app-window {
  background: var(--white);
  border: 1px solid var(--line-strong);
  border-radius: 14px;
  box-shadow: var(--shadow-md);
  overflow: hidden;
}

.app-window-bar {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 11px 16px;
  background: var(--surface-2);
  border-bottom: 1px solid var(--line);
}
.app-window-bar .win-mark { display: inline-flex; }
.app-window-bar .win-title {
  font-size: 12px;
  font-weight: 500;
  letter-spacing: -0.01em;
  color: var(--navy);
  opacity: 0.85;
}

.app-window-body { padding: 18px; }

/* Status pills */
.pill {
  display: inline-flex;
  align-items: center;
  gap: 5px;
  height: 22px;
  padding: 0 9px;
  border-radius: 999px;
  font-size: 11px;
  font-weight: 500;
  letter-spacing: 0.01em;
  white-space: nowrap;
}
.pill-green { background: var(--green-soft); color: var(--green); }
.pill-amber { background: var(--gold-soft); color: #8a6a00; }
.pill-red   { background: var(--red-soft);  color: var(--red); }
.pill-grey  { background: var(--navy-tint); color: var(--warm-grey); }
```

- [ ] **Step 3: Verify the CSS parses (no broken page)**

Run: `python3 -m http.server 8000` (from repo root, leave running) then open `http://localhost:8000/`.
Expected: page still renders normally (no mockups yet — that's Task 2+). Confirm no obvious layout breakage and that the stylesheet loaded (nav/hero look unchanged).

- [ ] **Step 4: Commit**

```bash
git add styles.css
git commit -m "feat(home): add feature-row, app-window and pill styles + accent tokens"
```

---

## Task 2: Restructure the "What it does" section into four feature rows

This task swaps the markup structure and fixes the copy. Mockup bodies are added as empty `.app-window-body` placeholders here, then filled in Tasks 3–6 so each mockup is verified independently.

**Files:**
- Modify: `index.html` (replace `<section id="what" class="modules">…</section>`, ~lines 53–116)

- [ ] **Step 1: Replace the section markup**

In `index.html`, replace the entire `<section id="what" class="modules"> … </section>` block with:

```html
    <section id="what" class="features-section">
      <div class="section-head">
        <div class="eyebrow">What it does</div>
        <h2 class="section-title">Four modules. One source of truth.</h2>
      </div>

      <div class="features">

        <!-- Rostering -->
        <article class="feature-row">
          <div class="feature-copy">
            <div class="module-icon" aria-hidden="true">
              <svg viewBox="0 0 24 24" width="22" height="22" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round">
                <rect x="3" y="5" width="18" height="16" rx="2" />
                <path d="M16 3v4M8 3v4M3 10h18" />
              </svg>
            </div>
            <h3 class="module-title">Rostering</h3>
            <p class="module-body">
              Drag shifts, copy weeks, see award-correct wage cost as you build.
              Publishing pushes a notification to every staff member.
            </p>
          </div>
          <div class="feature-mock">
            <div class="app-window">
              <div class="app-window-bar">
                <span class="win-mark" aria-hidden="true">
                  <svg viewBox="0 0 32 32" width="16" height="16" fill="none">
                    <path d="M3 26 L13 8 L18 17" stroke="#FFC857" stroke-width="3.5" stroke-linecap="round" stroke-linejoin="round"/>
                    <path d="M11 26 L21 8 L29 26" stroke="#FF6B35" stroke-width="3.5" stroke-linecap="round" stroke-linejoin="round"/>
                  </svg>
                </span>
                <span class="win-title">Rostering · Week of 30 Jun</span>
              </div>
              <div class="app-window-body" data-mock="rostering"><!-- Task 3 --></div>
            </div>
          </div>
        </article>

        <!-- Timesheets -->
        <article class="feature-row reverse">
          <div class="feature-copy">
            <div class="module-icon" aria-hidden="true">
              <svg viewBox="0 0 24 24" width="22" height="22" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round">
                <circle cx="12" cy="12" r="9" />
                <path d="M12 7v5l3 2" />
              </svg>
            </div>
            <h3 class="module-title">Timesheets</h3>
            <p class="module-body">
              Clock-in via the iPad time clock, approve weekly, export straight to
              Xero. Award allowances applied automatically.
            </p>
          </div>
          <div class="feature-mock">
            <div class="app-window">
              <div class="app-window-bar">
                <span class="win-mark" aria-hidden="true">
                  <svg viewBox="0 0 32 32" width="16" height="16" fill="none">
                    <path d="M3 26 L13 8 L18 17" stroke="#FFC857" stroke-width="3.5" stroke-linecap="round" stroke-linejoin="round"/>
                    <path d="M11 26 L21 8 L29 26" stroke="#FF6B35" stroke-width="3.5" stroke-linecap="round" stroke-linejoin="round"/>
                  </svg>
                </span>
                <span class="win-title">Timesheets · Week of 23 Jun</span>
              </div>
              <div class="app-window-body" data-mock="timesheets"><!-- Task 4 --></div>
            </div>
          </div>
        </article>

        <!-- Purchasing -->
        <article class="feature-row">
          <div class="feature-copy">
            <div class="module-icon" aria-hidden="true">
              <svg viewBox="0 0 24 24" width="22" height="22" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round">
                <path d="M3 5h4l2 11h11l1.5-8H7" />
                <circle cx="10" cy="20" r="1.4" />
                <circle cx="17" cy="20" r="1.4" />
              </svg>
            </div>
            <h3 class="module-title">Purchasing</h3>
            <p class="module-body">
              Place orders by supplier or by product across stores. AI reads
              invoices, matches them to orders, posts to Xero as bills.
            </p>
          </div>
          <div class="feature-mock">
            <div class="app-window">
              <div class="app-window-bar">
                <span class="win-mark" aria-hidden="true">
                  <svg viewBox="0 0 32 32" width="16" height="16" fill="none">
                    <path d="M3 26 L13 8 L18 17" stroke="#FFC857" stroke-width="3.5" stroke-linecap="round" stroke-linejoin="round"/>
                    <path d="M11 26 L21 8 L29 26" stroke="#FF6B35" stroke-width="3.5" stroke-linecap="round" stroke-linejoin="round"/>
                  </svg>
                </span>
                <span class="win-title">Purchasing · Invoice reconcile</span>
              </div>
              <div class="app-window-body" data-mock="purchasing"><!-- Task 5 --></div>
            </div>
          </div>
        </article>

        <!-- Leave -->
        <article class="feature-row reverse">
          <div class="feature-copy">
            <div class="module-icon" aria-hidden="true">
              <svg viewBox="0 0 24 24" width="22" height="22" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round">
                <path d="M5 21V8a3 3 0 0 1 3-3h8a3 3 0 0 1 3 3v13" />
                <path d="M9 21V12h6v9" />
              </svg>
            </div>
            <h3 class="module-title">Leave</h3>
            <p class="module-body">
              Staff request leave, you approve or decline, balances update
              automatically. Requests, balances and approvals in one place.
            </p>
          </div>
          <div class="feature-mock">
            <div class="app-window">
              <div class="app-window-bar">
                <span class="win-mark" aria-hidden="true">
                  <svg viewBox="0 0 32 32" width="16" height="16" fill="none">
                    <path d="M3 26 L13 8 L18 17" stroke="#FFC857" stroke-width="3.5" stroke-linecap="round" stroke-linejoin="round"/>
                    <path d="M11 26 L21 8 L29 26" stroke="#FF6B35" stroke-width="3.5" stroke-linecap="round" stroke-linejoin="round"/>
                  </svg>
                </span>
                <span class="win-title">Leave · Requests</span>
              </div>
              <div class="app-window-body" data-mock="leave"><!-- Task 6 --></div>
            </div>
          </div>
        </article>

      </div>
    </section>
```

Note: the `.module-body` for Rostering also fixes the original copy typo ("pushes a push" → "pushes a notification").

- [ ] **Step 2: Verify structure and alternation**

Hard-refresh `http://localhost:8000/`.
Expected: four rows under "What it does". Rows 1 and 3 have copy on the left, empty window on the right; rows 2 and 4 (`.reverse`) have copy on the right, window on the left. Each window shows a title bar with the Crosby mark and screen name; bodies are empty for now. Fourth module reads "Leave" with no "HR" or onboarding text anywhere on the page.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat(home): convert module grid to alternating feature rows, rename Leave"
```

---

## Task 3: Rostering mockup (roster grid + wage-cost stat)

**Files:**
- Modify: `index.html` (`<div class="app-window-body" data-mock="rostering">`)
- Modify: `styles.css` (append rostering mockup classes to the Features block)

- [ ] **Step 1: Add the mockup markup**

Replace `<div class="app-window-body" data-mock="rostering"><!-- Task 3 --></div>` with:

```html
              <div class="app-window-body" data-mock="rostering">
                <div class="mk-roster">
                  <div class="mk-roster-grid">
                    <div class="mk-rcell mk-rhead"></div>
                    <div class="mk-rcell mk-rhead">Mon</div>
                    <div class="mk-rcell mk-rhead">Tue</div>
                    <div class="mk-rcell mk-rhead">Wed</div>
                    <div class="mk-rcell mk-rhead">Thu</div>
                    <div class="mk-rcell mk-rhead">Fri</div>

                    <div class="mk-rcell mk-rname">Maya R.</div>
                    <div class="mk-rcell"><span class="mk-shift sh-orange">7–3</span></div>
                    <div class="mk-rcell"><span class="mk-shift sh-orange">7–3</span></div>
                    <div class="mk-rcell"></div>
                    <div class="mk-rcell"><span class="mk-shift sh-orange">7–3</span></div>
                    <div class="mk-rcell"><span class="mk-shift sh-orange">7–3</span></div>

                    <div class="mk-rcell mk-rname">Jack T.</div>
                    <div class="mk-rcell"><span class="mk-shift sh-gold">3–11</span></div>
                    <div class="mk-rcell"></div>
                    <div class="mk-rcell"><span class="mk-shift sh-gold">3–11</span></div>
                    <div class="mk-rcell"><span class="mk-shift sh-gold">3–11</span></div>
                    <div class="mk-rcell"><span class="mk-shift sh-gold">3–11</span></div>

                    <div class="mk-rcell mk-rname">Priya S.</div>
                    <div class="mk-rcell"></div>
                    <div class="mk-rcell"><span class="mk-shift sh-navy">5–9</span></div>
                    <div class="mk-rcell"><span class="mk-shift sh-navy">5–9</span></div>
                    <div class="mk-rcell"></div>
                    <div class="mk-rcell"><span class="mk-shift sh-navy">5–9</span></div>
                  </div>
                  <div class="mk-cost">
                    <div>
                      <div class="mk-cost-label">Wage cost</div>
                      <div class="mk-cost-value">$4,820 <span class="mk-cost-pct">28.4%</span></div>
                    </div>
                    <span class="pill pill-green">● On budget</span>
                  </div>
                </div>
              </div>
```

- [ ] **Step 2: Add the rostering styles**

Append to the Features block in `styles.css`:

```css
/* Rostering mockup */
.mk-roster-grid {
  display: grid;
  grid-template-columns: 1.2fr repeat(5, 1fr);
  gap: 5px;
}
.mk-rcell {
  min-height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 11px;
  color: var(--navy);
  background: var(--off-white);
  border-radius: 6px;
}
.mk-rhead { background: transparent; color: var(--warm-grey); font-weight: 500; min-height: 20px; }
.mk-rname { justify-content: flex-start; padding-left: 8px; background: transparent; font-weight: 500; }
.mk-shift {
  width: 100%;
  margin: 0 2px;
  padding: 4px 0;
  border-radius: 6px;
  font-size: 11px;
  font-weight: 500;
  text-align: center;
}
.sh-orange { background: var(--orange-tint); color: var(--orange); }
.sh-gold   { background: var(--gold-soft);   color: #8a6a00; }
.sh-navy   { background: var(--navy-tint);   color: var(--navy); }

.mk-cost {
  margin-top: 14px;
  padding: 12px 14px;
  background: var(--surface-2);
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.mk-cost-label { font-size: 11px; color: var(--warm-grey); }
.mk-cost-value { font-size: 18px; font-weight: 600; letter-spacing: -0.01em; }
.mk-cost-pct { font-size: 13px; font-weight: 500; color: var(--warm-grey); }
```

- [ ] **Step 3: Verify**

Hard-refresh. Expected: in row 1's window, a 3-staff × 5-day grid with colored shift chips, and below it a "Wage cost $4,820 · 28.4%" card with a green "On budget" pill. No overflow past the window edge.

- [ ] **Step 4: Commit**

```bash
git add index.html styles.css
git commit -m "feat(home): rostering mockup with roster grid and wage-cost stat"
```

---

## Task 4: Timesheets mockup (week list + Export to Xero)

**Files:**
- Modify: `index.html` (`data-mock="timesheets"`)
- Modify: `styles.css` (append timesheets classes)

- [ ] **Step 1: Add the mockup markup**

Replace `<div class="app-window-body" data-mock="timesheets"><!-- Task 4 --></div>` with:

```html
              <div class="app-window-body" data-mock="timesheets">
                <div class="mk-rows">
                  <div class="mk-row">
                    <span class="mk-row-name">Maya R.</span>
                    <span class="mk-row-meta">38.0 hrs</span>
                    <span class="pill pill-green">Approved</span>
                  </div>
                  <div class="mk-row">
                    <span class="mk-row-name">Jack T.</span>
                    <span class="mk-row-meta">32.5 hrs</span>
                    <span class="pill pill-green">Approved</span>
                  </div>
                  <div class="mk-row">
                    <span class="mk-row-name">Priya S.</span>
                    <span class="mk-row-meta">21.0 hrs</span>
                    <span class="pill pill-amber">Pending</span>
                  </div>
                  <div class="mk-row">
                    <span class="mk-row-name">Liam W.</span>
                    <span class="mk-row-meta">40.0 hrs</span>
                    <span class="pill pill-green">Approved</span>
                  </div>
                </div>
                <div class="mk-foot">
                  <span class="mk-foot-meta">131.5 hrs · 4 staff</span>
                  <span class="mk-btn">Export to Xero</span>
                </div>
              </div>
```

- [ ] **Step 2: Add the timesheets/shared-row styles**

Append to the Features block in `styles.css`:

```css
/* Shared list rows (timesheets + leave) */
.mk-rows { display: grid; gap: 6px; }
.mk-row {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 12px;
  background: var(--off-white);
  border-radius: 8px;
  font-size: 13px;
}
.mk-row-name { font-weight: 500; flex: 1; }
.mk-row-meta { color: var(--warm-grey); font-size: 12px; }

.mk-foot {
  margin-top: 14px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.mk-foot-meta { font-size: 12px; color: var(--warm-grey); }
.mk-btn {
  display: inline-flex;
  align-items: center;
  height: 32px;
  padding: 0 14px;
  border-radius: 8px;
  font-size: 12px;
  font-weight: 500;
  color: var(--white);
  background:
    radial-gradient(circle at 88% -20%, rgba(255, 200, 87, 0.55), transparent 55%),
    var(--orange);
}
```

- [ ] **Step 3: Verify**

Hard-refresh. Expected: row 2's window shows four staff rows with hours and Approved/Pending pills, and a footer "131.5 hrs · 4 staff" with an orange "Export to Xero" button.

- [ ] **Step 4: Commit**

```bash
git add index.html styles.css
git commit -m "feat(home): timesheets mockup with approval pills and Xero export"
```

---

## Task 5: Purchasing mockup (invoice reconcile)

**Files:**
- Modify: `index.html` (`data-mock="purchasing"`)
- Modify: `styles.css` (append purchasing classes)

- [ ] **Step 1: Add the mockup markup**

Replace `<div class="app-window-body" data-mock="purchasing"><!-- Task 5 --></div>` with:

```html
              <div class="app-window-body" data-mock="purchasing">
                <div class="mk-inv-head">
                  <div>
                    <div class="mk-inv-supplier">Bidfood</div>
                    <div class="mk-inv-ref">INV-20418 · matched to PO-1192</div>
                  </div>
                  <span class="pill pill-green">✓ Matched</span>
                </div>
                <div class="mk-inv-lines">
                  <div class="mk-inv-line"><span>Olive oil 4L × 6</span><span>$214.80</span></div>
                  <div class="mk-inv-line"><span>Flour 12.5kg × 8</span><span>$176.00</span></div>
                  <div class="mk-inv-line"><span>Tomatoes tin × 24</span><span>$92.40</span></div>
                </div>
                <div class="mk-inv-totals">
                  <div class="mk-inv-trow"><span>Subtotal</span><span>$483.20</span></div>
                  <div class="mk-inv-trow"><span>GST</span><span>$48.32</span></div>
                  <div class="mk-inv-trow mk-inv-total"><span>Total</span><span>$531.52</span></div>
                </div>
                <div class="mk-foot">
                  <span class="mk-foot-meta">No variance</span>
                  <span class="mk-btn">Post to Xero</span>
                </div>
              </div>
```

- [ ] **Step 2: Add the purchasing styles**

Append to the Features block in `styles.css`:

```css
/* Purchasing / invoice reconcile mockup */
.mk-inv-head {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding-bottom: 12px;
  border-bottom: 1px solid var(--line);
}
.mk-inv-supplier { font-size: 14px; font-weight: 600; }
.mk-inv-ref { font-size: 11px; color: var(--warm-grey); margin-top: 2px; }
.mk-inv-lines { display: grid; gap: 8px; padding: 12px 0; }
.mk-inv-line {
  display: flex;
  justify-content: space-between;
  font-size: 12px;
  color: rgba(15, 31, 61, 0.78);
}
.mk-inv-totals {
  display: grid;
  gap: 6px;
  padding: 12px 0;
  border-top: 1px solid var(--line);
}
.mk-inv-trow {
  display: flex;
  justify-content: space-between;
  font-size: 12px;
  color: var(--warm-grey);
}
.mk-inv-total { font-size: 14px; font-weight: 600; color: var(--navy); }
```

- [ ] **Step 3: Verify**

Hard-refresh. Expected: row 3's window shows "Bidfood · INV-20418 matched to PO-1192" with a green "✓ Matched" pill, three line items, Subtotal/GST/Total rows, and a "Post to Xero" button.

- [ ] **Step 4: Commit**

```bash
git add index.html styles.css
git commit -m "feat(home): purchasing mockup with invoice reconcile"
```

---

## Task 6: Leave mockup (requests + balances)

**Files:**
- Modify: `index.html` (`data-mock="leave"`)
- Modify: `styles.css` (append leave classes)

- [ ] **Step 1: Add the mockup markup**

Replace `<div class="app-window-body" data-mock="leave"><!-- Task 6 --></div>` with:

```html
              <div class="app-window-body" data-mock="leave">
                <div class="mk-rows">
                  <div class="mk-row mk-row-col">
                    <div class="mk-leave-top">
                      <span class="mk-row-name">Maya R.</span>
                      <span class="pill pill-amber">Pending</span>
                    </div>
                    <div class="mk-leave-meta">Annual · 14–18 Jul · 5 days · balance 62 hrs</div>
                  </div>
                  <div class="mk-row mk-row-col">
                    <div class="mk-leave-top">
                      <span class="mk-row-name">Jack T.</span>
                      <span class="pill pill-green">Approved</span>
                    </div>
                    <div class="mk-leave-meta">Personal · 2 Jul · 1 day · balance 18 hrs</div>
                  </div>
                  <div class="mk-row mk-row-col">
                    <div class="mk-leave-top">
                      <span class="mk-row-name">Priya S.</span>
                      <span class="pill pill-red">Declined</span>
                    </div>
                    <div class="mk-leave-meta">Annual · 30 Jun–4 Jul · clashes with cover</div>
                  </div>
                </div>
                <div class="mk-foot">
                  <span class="mk-foot-meta">1 awaiting approval</span>
                  <span class="mk-btn">Review</span>
                </div>
              </div>
```

- [ ] **Step 2: Add the leave styles**

Append to the Features block in `styles.css`:

```css
/* Leave mockup (reuses .mk-rows / .mk-row) */
.mk-row-col { flex-direction: column; align-items: stretch; gap: 6px; }
.mk-leave-top { display: flex; align-items: center; justify-content: space-between; }
.mk-leave-meta { font-size: 11px; color: var(--warm-grey); }
```

- [ ] **Step 3: Verify**

Hard-refresh. Expected: row 4's window shows three leave requests with Pending/Approved/Declined pills (amber/green/red), leave type + date + balance text, and a footer "1 awaiting approval · Review".

- [ ] **Step 4: Commit**

```bash
git add index.html styles.css
git commit -m "feat(home): leave mockup with requests, balances and statuses"
```

---

## Task 7: Responsive + full-page verification

**Files:**
- Modify: `styles.css` (`@media (max-width: 760px)` block, ~line 453)

- [ ] **Step 1: Add responsive rules**

Inside the existing `@media (max-width: 760px)` block in `styles.css`, add:

```css
  .features { padding: 0 16px; margin-bottom: 64px; gap: 48px; }
  .feature-row { grid-template-columns: 1fr; gap: 24px; }
  .feature-row.reverse .feature-copy { order: 1; }
  .feature-row.reverse .feature-mock { order: 2; }
  .feature-copy { max-width: none; }
  .app-window-body { padding: 14px; }
```

- [ ] **Step 2: Verify desktop (~1200px)**

Hard-refresh at a wide window. Expected: four rows, alternating image side, all four mockups populated and contained within their windows. Nav/hero/built-for/contact/footer unchanged.

- [ ] **Step 3: Verify mobile (~375px)**

Resize the browser to ~375px wide (or device toolbar). Expected: every row stacks to a single column with **copy first, mockup second** (including the `.reverse` rows). No horizontal scrollbar anywhere. Roster grid and all mockup text remain legible and inside their windows.

- [ ] **Step 4: Verify no "HR" anywhere**

Run: `grep -ri "hr\b\|onboard" index.html` (from repo root).
Expected: no matches referencing HR or onboarding in visible copy. (Confirm the fourth module reads "Leave".)

- [ ] **Step 5: Commit**

```bash
git add styles.css
git commit -m "feat(home): responsive feature rows for mobile"
```

---

## Self-Review (completed by plan author)

- **Spec coverage:** Alternating rows (Task 2); app-window frame (Task 1 CSS + Task 2 markup); four mockups with real labels — rostering/wage-cost (Task 3), timesheets/Xero (Task 4), purchasing/reconcile (Task 5), leave (Task 6); green/red tokens (Task 1); rename Leave & HR → Leave and remove HR copy (Task 2 + Task 7 grep); responsive stacking copy-first (Task 7); verification at desktop+mobile (Task 7). All spec sections mapped.
- **Placeholders:** Empty `data-mock` divs in Task 2 are intentional and filled in Tasks 3–6; no "TODO"/"TBD" left in shipped code.
- **Class consistency:** `.mk-rows`/`.mk-row`/`.mk-foot`/`.mk-btn` defined in Task 4 and reused in Tasks 5–6; `.pill*` defined in Task 1 and used throughout; `.feature-row.reverse` order rules defined in Task 1 and overridden in Task 7. Consistent.
