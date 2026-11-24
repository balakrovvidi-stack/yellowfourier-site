# Yellow Fourier Research Labs — public website (GitHub Pages)

The **public** pages of yellowfourier.ai, hosted on GitHub Pages. Static HTML/CSS, no build step.

> **Payments are NOT in this repo, on purpose.** The enrollment/payment page
> (`enroll.html`) and its server code (`get-token.php`) stay on **A2 Hosting**
> under `enroll.yellowfourier.ai`. Email also stays on A2. This repo must never
> contain `get-token.php` or any Authorize.Net keys.

## Pages here
- `index.html` — Home
- `program.html` — Program
- `resources.html` — Resources
- `faq.html` — FAQ
- `apply.html` — Register for orientation (its "Enroll" button links out to the A2 payment page)
- `terms.html` — Terms & Enrollment Agreement
- `assets/styles.css` — shared styling
- `robots.txt`, `sitemap.xml` — SEO
- `CNAME` — custom domain for GitHub Pages (yellowfourier.ai)

## Deploy to GitHub Pages
1. Create a repo (e.g. `yellowfourier-site`) and push these files to the `main` branch:
   ```
   git remote add origin https://github.com/<you>/yellowfourier-site.git
   git push -u origin main
   ```
2. In the repo: **Settings → Pages → Build and deployment → Source: Deploy from a branch**, Branch: `main` / root. Save.
3. Settings → Pages → **Custom domain**: `yellowfourier.ai` (the CNAME file already sets this). Tick **Enforce HTTPS** once it's available.

## DNS — the important part (keep email + payments on A2)
Email and the payment subdomain stay on A2; only the **web** records for the
main domain move to GitHub. In your DNS provider:

- **MX records → leave exactly as they are (A2).** Do not touch these — this is what keeps `admissions@yellowfourier.ai` working.
- **Apex `yellowfourier.ai` (web) → GitHub Pages.** Set four A records to GitHub's IPs:
  `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
  (and optionally the matching AAAA records).
- **`www` → GitHub Pages.** CNAME `www` → `<you>.github.io`.
- **`enroll` → A2.** Add a record for `enroll.yellowfourier.ai` pointing to A2
  (A record to A2's IP, or CNAME to the A2 host). This is where `enroll.html` +
  `get-token.php` live. The Register page's "Enroll" button points here.

### Cutover (zero payment risk)
1. Push + enable GitHub Pages first; verify it on the temporary `*.github.io` URL.
2. Point `enroll.yellowfourier.ai` at A2 and confirm the Pay Now flow works there.
3. Only then switch the apex/www web records to GitHub. MX (email) and the
   enroll subdomain (payments) never change during this, so neither is disrupted.

## A2 side (for Ramm)
A2 keeps serving the payment page. Make `enroll.yellowfourier.ai` serve the
existing `enroll.html` + `get-token.php` + `assets/styles.css` (already there).
Nothing about the payment code changes — only the subdomain it answers on.

## Editing from one place (this repo = source of truth)
All the site's pages now live here, including `enroll.html`, so changes can be
made in one place, committed, and deployed. Day-to-day: edit the HTML/CSS,
`git commit`, `git push` — GitHub Pages redeploys the public pages in ~1 minute.

- The **public pages** (Home, Program, Resources, FAQ, Apply/register, Terms)
  are served by **GitHub Pages** on `yellowfourier.ai`.
- **`enroll.html`** is the payment page. Its real home is **A2**
  (`enroll.yellowfourier.ai`), where `get-token.php` runs. The copy here is the
  editable source — when you change it, the updated file is also deployed to A2.
  Its nav/footer links are absolute to `https://yellowfourier.ai/...` so it works
  the same from either host. It is `Disallow`ed in robots so the GitHub-Pages
  copy is not indexed (the A2 subdomain is the live one).

## SECURITY — do not commit secrets
- **`get-token.php` is NOT in this repo and must never be.** It holds the
  Authorize.Net **API Login ID + Transaction Key** and lives **only on A2**.
  It is listed in `.gitignore` so it cannot be committed by accident.
- Never put API keys, `.env`, or `config.php` in this repo. GitHub repos (even
  private ones) are the wrong place for live payment credentials.
