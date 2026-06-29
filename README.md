# crosbyops.com landing

Static landing page for **crosbyops.com**. Completely separate from the app
at `app.crosbyops.com` (that lives in the `kicco-ops` repo) so a copy tweak
here cannot break the app and vice versa.

Pure HTML + CSS, no build step.

## Local preview

Open `index.html` in a browser, or run a tiny static server:

```bash
python3 -m http.server 4000
# then visit http://localhost:4000
```

## Deploy to GitHub Pages

1. **Create a new GitHub repo** (e.g. `tysoncrosby/crosbyops-landing`).
2. **Push this directory** to it:
   ```bash
   cd /Users/tysoncrosby/Documents/GitHub/crosbyops-landing
   git init
   git add .
   git commit -m "Initial landing page"
   git branch -M main
   git remote add origin git@github.com:tysoncrosby/crosbyops-landing.git
   git push -u origin main
   ```
3. **Enable Pages** in the repo:
   GitHub → Settings → Pages → Build and deployment → Source = `main` / `(root)`.
4. **Custom domain**: the `CNAME` file in this repo already contains
   `crosbyops.com`, so Pages picks it up automatically.
5. **Point GoDaddy DNS at GitHub**. In GoDaddy DNS for `crosbyops.com`:
   - **A records** for `@`, all four GitHub Pages IPs:
     ```
     185.199.108.153
     185.199.109.153
     185.199.110.153
     185.199.111.153
     ```
   - **CNAME** for `www` → `tysoncrosby.github.io`
   - Leave the existing `app` CNAME alone (that points to Vercel for the app).
6. Wait 10-60 minutes for DNS to propagate, then in GitHub Pages settings
   tick **Enforce HTTPS**.

That is it. Every `git push` to `main` redeploys automatically.

## Editing

- Copy on the home page lives in `index.html`
- The request-access form lives in `apply.html`
- Visuals live in `styles.css`
- Favicon set is copied from the app so the two stay in sync

Brand tokens at the top of `styles.css` mirror the app's tokens so the
two sites stay visually consistent.

## Wire up the request-access form

`apply.html` currently posts to `mailto:hello@crosbyops.com` as a
zero-setup fallback. That works but the UX is rough (opens the user's
email client). When you want a proper inbox + database of leads, swap
in a free form-handler. Recommended: **Formspree**.

1. Sign up at https://formspree.io (free tier: 50 submissions/month).
2. Create a new form, copy the action URL (looks like
   `https://formspree.io/f/abcdwxyz`).
3. In `apply.html`, change:
   ```html
   <form
     class="apply-form"
     action="mailto:hello@crosbyops.com"
     method="POST"
     enctype="text/plain"
   >
   ```
   to:
   ```html
   <form
     class="apply-form"
     action="https://formspree.io/f/abcdwxyz"
     method="POST"
   >
   ```
4. Commit + push. Done · submissions land in your Formspree dashboard
   and email.

Alternatives if you do not want Formspree: Web3Forms, Getform, Basin,
or roll your own with a Cloudflare Worker.
