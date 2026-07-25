# CrosCheq-esque Page Chrome — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Give crosbyops.com's page chrome the alternating section-background rhythm and dark navy footer documented in the CrosCheq brand guide, without touching copy, content, the hero, or adding the CrosCheq name/wordmark anywhere.

**Architecture:** Pure HTML/CSS edits to `index.html` and `styles.css`. No JS, no build step, no new image assets. Two of the six sections between the hero and the footer (`.more`, `.integrations`) move from a constrained centered block to a full-bleed section with an inner `.section-inner` wrapper, so their background can span the full browser width like `.built-for` already does. The footer gets recoloured to the same navy gradient the hero card already uses, plus the existing tick-mark SVG (already used in the nav brand lockup) added inline.

**Tech Stack:** Static HTML5, hand-written CSS (custom properties, grid/flexbox). Served as-is via GitHub Pages.

**Note on testing:** This is a static marketing site with no test runner. The "verify" step in each task is a browser check — start `python3 -m http.server 8000` from the repo root, open `http://localhost:8000/`, hard-refresh (the site's stylesheet is cache-busted via `?v=N`, bumped in the final task).

**Spec:** `docs/superpowers/specs/2026-07-25-croscheq-esque-chrome-design.md`

---

## File Structure

- **Modify** `styles.css`:
  - `.more` (line ~763) and `.integrations` (line ~731) — replace the constrained-block rules with a full-bleed section + `.section-inner` wrapper rule.
  - `.footer` / `.footer-brand` / `.footer-meta` (lines ~934–964) — recolour to the dark navy gradient.
- **Modify** `index.html`:
  - `<section id="more">` (line ~772) and `<section id="integrations">` (line ~870) — wrap existing inner content in a new `<div class="section-inner">`.
  - `.footer-brand` (line ~955) — add the tick-mark SVG before the "Crosby Ops" text.
  - `styles.css?v=9` (line 14) → `?v=10`.
- **Modify** `apply.html`:
  - Same `styles.css?v=9` → `?v=10` bump (line 14), since it shares the stylesheet.

No new files. `.more-grid`, `.integ-grid`, and all card-level markup (`.more-card`, `.integ-card`, etc.) are unchanged — only their containing section's background and width handling changes.

---

## Task 1: Full-bleed background for the "And everything else" section

**Files:**
- Modify: `styles.css:763-767`

- [ ] **Step 1: Replace the `.more` rule**

Find this in `styles.css`:

```css
.more {
  max-width: 1200px;
  margin: 0 auto 96px;
  padding: 0 32px;
}
```

Replace it with:

```css
.more {
  background: var(--surface-2);
  padding: 80px 0;
  margin-bottom: 96px;
}
.more .section-inner {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 32px;
}
```

- [ ] **Step 2: Commit**

```bash
git add styles.css
git commit -m "Give the 'everything else' section a full-bleed warm background"
```

*(Visual verification happens in Task 3, after the matching HTML wrapper is added — the background won't show correctly until then since `.more-grid` currently has no `max-width`/padding of its own.)*

---

## Task 2: Full-bleed section shell for "Integrations"

**Files:**
- Modify: `styles.css:731-735`

- [ ] **Step 1: Replace the `.integrations` rule**

Find this in `styles.css`:

```css
.integrations {
  max-width: 1200px;
  margin: 0 auto 96px;
  padding: 0 32px;
}
```

Replace it with:

```css
.integrations {
  padding: 80px 0;
  margin-bottom: 96px;
}
.integrations .section-inner {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 32px;
}
```

Note: no `background` is set here — `.integrations` stays visually identical to today (it inherits the body's `var(--off-white)`). This is what makes it read as the "plain" band between the "More" section (now `--surface-2`) and "Built for" (already `--surface-2`).

- [ ] **Step 2: Commit**

```bash
git add styles.css
git commit -m "Convert Integrations to the same full-bleed section shell"
```

---

## Task 3: Wrap the HTML content of both sections in `.section-inner`

**Files:**
- Modify: `index.html:772` (open), `index.html:868` (close, just before `</section>`)
- Modify: `index.html:870` (open), `index.html:917` (close, just before `</section>`)

- [ ] **Step 1: Wrap the "More" section's content**

Find in `index.html`:

```html
    <section id="more" class="more">
      <div class="section-head">
```

Replace with:

```html
    <section id="more" class="more">
      <div class="section-inner">
      <div class="section-head">
```

Then find the end of that same section:

```html
          <h3 class="more-title">Multi-store control</h3>
          <p class="more-body">Switch stores, compare locations and roll up the group.</p>
        </article>
      </div>
    </section>
```

Replace with (adds the closing `</div>` for `.section-inner` right before `</section>`):

```html
          <h3 class="more-title">Multi-store control</h3>
          <p class="more-body">Switch stores, compare locations and roll up the group.</p>
        </article>
      </div>
      </div>
    </section>
```

- [ ] **Step 2: Wrap the "Integrations" section's content**

Find in `index.html`:

```html
    <section id="integrations" class="integrations">
      <div class="section-head">
        <div class="eyebrow">Integrations</div>
```

Replace with:

```html
    <section id="integrations" class="integrations">
      <div class="section-inner">
      <div class="section-head">
        <div class="eyebrow">Integrations</div>
```

Then find the end of that section:

```html
          <h3 class="integ-name">More coming soon</h3>
          <p class="integ-body">
            We are adding integrations all the time. Tell us the tools your
            venue runs on and we will wire them in.
          </p>
        </article>
      </div>
    </section>
```

Replace with:

```html
          <h3 class="integ-name">More coming soon</h3>
          <p class="integ-body">
            We are adding integrations all the time. Tell us the tools your
            venue runs on and we will wire them in.
          </p>
        </article>
      </div>
      </div>
    </section>
```

- [ ] **Step 3: Verify in the browser**

```bash
cd /Users/tysoncrosby/Documents/GitHub/crosbyops-landing
python3 -m http.server 8000
```

Open `http://localhost:8000/`, hard-refresh, scroll to "And everything else". Expected:
- The section now has a warm cream background (`#F4F3EC`) spanning the full browser width, not just a centered block.
- "Integrations" (the next section down) is back to the page's off-white, full width.
- The 3×3 card grid and all card content look exactly as before — same width, same wrapping, same spacing. Only the area *behind* the content changed.
- No horizontal scrollbar at any width (resize the browser narrower to confirm — the `.section-inner` padding should keep content from touching the edges).

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "Wrap More/Integrations content in .section-inner for full-bleed backgrounds"
```

---

## Task 4: Dark navy footer

**Files:**
- Modify: `styles.css:934-964`

- [ ] **Step 1: Replace the footer CSS block**

Find in `styles.css`:

```css
.footer {
  border-top: 1px solid var(--line);
  background: var(--white);
}

.footer-inner {
  max-width: 1200px;
  margin: 0 auto;
  padding: 32px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  flex-wrap: wrap;
}

.footer-brand {
  font-size: 14px;
  font-weight: 500;
  letter-spacing: -0.01em;
}

.footer-meta {
  display: flex;
  align-items: center;
  gap: 24px;
  font-size: 13px;
  color: var(--warm-grey);
}
.footer-meta a { color: var(--warm-grey); transition: color 0.15s; }
.footer-meta a:hover { color: var(--navy); }
```

Replace with:

```css
.footer {
  background: linear-gradient(135deg, var(--navy) 0%, var(--navy-2) 100%);
  color: var(--white);
}

.footer-inner {
  max-width: 1200px;
  margin: 0 auto;
  padding: 32px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  flex-wrap: wrap;
}

.footer-brand {
  font-size: 14px;
  font-weight: 500;
  letter-spacing: -0.01em;
  color: var(--white);
  display: inline-flex;
  align-items: center;
  gap: 8px;
}

.footer-meta {
  display: flex;
  align-items: center;
  gap: 24px;
  font-size: 13px;
  color: rgba(255, 255, 255, 0.55);
}
.footer-meta a { color: rgba(255, 255, 255, 0.55); transition: color 0.15s; }
.footer-meta a:hover { color: var(--white); }
```

(This drops the old `border-top: 1px solid var(--line)` — a hairline border doesn't read against a dark gradient. The gradient itself provides the visual separation from the section above.)

- [ ] **Step 2: Commit**

```bash
git add styles.css
git commit -m "Recolour the footer to the navy gradient used in the hero"
```

---

## Task 5: Add the tick mark to the footer brand

**Files:**
- Modify: `index.html:955`

- [ ] **Step 1: Add the SVG mark**

Find in `index.html`:

```html
      <div class="footer-brand">Crosby Ops</div>
```

Replace with:

```html
      <div class="footer-brand">
        <svg viewBox="0 0 32 32" width="18" height="18" fill="none" aria-hidden="true">
          <path d="M3 26 L13 8 L18 17" stroke="#FFC857" stroke-width="3.5" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M11 26 L21 8 L29 26" stroke="#FF6B35" stroke-width="3.5" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
        Crosby Ops
      </div>
```

This is the exact same two-path mark already used in the site nav (`.brand-mark` in the header) — same viewBox, same colours, same stroke widths. Copy it verbatim rather than re-deriving it, so the mark stays pixel-identical across the page.

- [ ] **Step 2: Verify in the browser**

Refresh `http://localhost:8000/`, scroll to the footer. Expected:
- Footer background is the navy-to-navy-2 diagonal gradient, matching the hero card's colour (not necessarily its glow — the footer has no radial highlight, just the flat gradient).
- The tick mark appears immediately to the left of "Crosby Ops", vertically centred, small (18px), matching the nav mark's proportions.
- "Crosby Ops" text and the four footer links (Sign in / Support / Privacy / Terms) are legible — text white, links at ~55% white with a hover state to full white.
- Resize to mobile width (375px): footer content wraps without overlapping or overflowing horizontally.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "Add the tick mark to the footer brand lockup"
```

---

## Task 6: Bump the cache-busting version and do a full-page pass

**Files:**
- Modify: `index.html:14`
- Modify: `apply.html:14`

- [ ] **Step 1: Bump the version**

In both `index.html` and `apply.html`, find:

```html
  <link rel="stylesheet" href="styles.css?v=9" />
```

Replace with:

```html
  <link rel="stylesheet" href="styles.css?v=10" />
```

- [ ] **Step 2: Full-page verification**

```bash
python3 -m http.server 8000
```

Open `http://localhost:8000/?cb=1` (cache-bust the HTML fetch too) at desktop width (1280px+) and walk the whole page top to bottom:

1. **Hero** — unchanged. Navy gradient card, radial orange glow, dashboard mockup. If this looks any different from before this plan started, something leaked — check `.hero` wasn't touched.
2. **"What it does"** — off-white, unchanged.
3. **"And everything else"** — now on a full-width warm cream band.
4. **Integrations** — back to off-white, full width.
5. **Built for** — warm cream (unchanged from before).
6. **Get started (contact)** — off-white, unchanged.
7. **Footer** — dark navy gradient, tick mark + "Crosby Ops", four links, all legible.

Then resize to mobile (375px) and repeat the same walk — confirm no horizontal scrollbar anywhere (check via the same technique used earlier this project: `document.documentElement.scrollWidth === window.innerWidth` in the browser console) and that the "More"/"Integrations" full-bleed bands still look right at narrow width (padding shouldn't collapse to zero).

- [ ] **Step 3: Commit**

```bash
git add index.html apply.html
git commit -m "Bump styles.css cache-busting version to v10"
```

---

## Task 7: Ship it

- [ ] **Step 1: Push and open a PR**

```bash
git push -u origin HEAD
gh pr create --title "CrosCheq-esque page chrome: section rhythm + dark footer" --body "$(cat <<'EOF'
## Summary
- Alternating section background rhythm (off-white / warm cream) down the page, matching the pattern the CrosCheq brand guide uses — grounded in the actual guide, not the coming-soon page.
- Footer recoloured to the same navy gradient as the hero, with the tick mark added to the brand lockup.
- No copy changes, no CrosCheq name/wordmark anywhere, no new colours (reuses existing --surface-2/--navy/--navy-2 tokens).

Spec: docs/superpowers/specs/2026-07-25-croscheq-esque-chrome-design.md

## Test plan
- [x] Verified full page flow at desktop width
- [x] Verified full page flow at mobile width (375px), no horizontal overflow
- [x] Hero confirmed unchanged

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

- [ ] **Step 2: Merge**

```bash
gh pr merge --squash --delete-branch
```

- [ ] **Step 3: Verify live**

```bash
for i in 1 2 3 4 5; do
  if curl -s "https://crosbyops.com/styles.css?cb=$i" | grep -q "linear-gradient(135deg, var(--navy) 0%, var(--navy-2) 100%);"; then
    echo "LIVE"
    break
  fi
  sleep 15
done
```
