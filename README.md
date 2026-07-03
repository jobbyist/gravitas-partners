# Gravitas Founding Members Program 2026

A landing page and application flow for the Gravitas Founding Members Program — a
private, 10-seat syndicate offering pooled SPV equity in two Gravitas Industries
ventures: **Jobbyist Africa** ($250/seat, 3% pooled equity) and **SkinLabs® South
Africa** ($125/seat, 5% pooled equity).

**Live structure:**
- `index.html` — the main landing page (hero, opportunity cards, governance
  diagram, projections, compliance timeline, FAQ, footer disclaimers).
- `apply.html` — a five-step application flow that collects applicant details,
  lets them choose their asset(s), and captures an e-signature against the NDA
  and Indemnity Form before submission.
- `docs/NDA.pdf` and `docs/Indemnity-Form.pdf` — the two downloadable legal
  documents referenced in the application flow.

---

## Quick start

This is a static site with no build step. To preview locally:

```bash
# from the repo root
python3 -m http.server 8000
# then open http://localhost:8000
```

Or just open `index.html` directly in a browser (the network canvas, forms,
and signature pads all work from `file://` too — only the Google Fonts CDN
request needs live internet access).

## Deploying

Any static host works. The simplest path is **GitHub Pages**:

1. Push this repo to GitHub (see "Pushing to GitHub" below).
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to `Deploy from a branch`,
   branch `main`, folder `/ (root)`.
4. Your site will be live at `https://<your-username>.github.io/<repo-name>/`
   within a minute or two.

If you want it on `fm.gravitas.uno` or another custom domain, add a `CNAME`
file at the repo root containing just the domain, and point your DNS `A`/`CNAME`
records at GitHub Pages per [GitHub's custom domain docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site).

## Wiring up the application form's backend

Right now `assets/js/apply.js` runs in **local-only demo mode**: every
submission validates, e-signs, shows a confirmation with a reference number,
and is logged to `localStorage` under the `gfm_applications` key and to the
browser console — but nothing leaves the browser yet.

To actually receive applications, open `assets/js/apply.js` and set:

```js
const FORM_ENDPOINT = "https://formspree.io/f/your-form-id";
```

to any endpoint that accepts a JSON POST — Formspree (same pattern used on
the Jobbyist pages), a Supabase Edge Function, a Zapier "Catch Hook", or your
own API. The payload shape is:

```json
{
  "reference": "GFM-2026-AB12C",
  "submittedAt": "2026-07-03T12:00:00.000Z",
  "fullName": "…",
  "email": "…",
  "phone": "…",
  "country": "…",
  "selectedAssets": ["jobbyist", "skinlabs"],
  "totalBidUSD": 375,
  "ndaSignatureDataUrl": "data:image/png;base64,…",
  "indemnitySignatureDataUrl": "data:image/png;base64,…"
}
```

The two `*SignatureDataUrl` fields are base64 PNG images of the applicant's
drawn signature, suitable for embedding directly into a generated PDF or
stored as an attachment.

## Regenerating the legal PDFs

`docs/NDA.pdf` and `docs/Indemnity-Form.pdf` were generated with
[reportlab](https://www.reportlab.com/) from a Python script. If you need to
edit the legal text, regenerate them from source rather than editing the PDFs
directly — ask your assistant to update the generating script and re-run it,
or edit the clauses directly with any PDF-authoring tool of your choice.

## Editable numbers

A few figures are hard-coded in both `index.html` and `assets/js/apply.js`
and should be kept in sync if terms change:

| Value | Where |
|---|---|
| Seat price ($250 / $125) | `index.html` hero, opportunity cards; `apply.html` asset options; `apply.js` `ASSET_PRICES` |
| Seats filled / remaining | `assets/js/main.js` → `SEATS_FILLED` constant |
| USD:ZAR planning rate (16.50) | `index.html` disclaimer + cards; `apply.js` `ZAR_RATE` |
| Memorandum link | `https://partners.gravitas.uno/investment-memorandum-2026.pdf` (search both HTML files) |

## Compliance notes

This site is built to reflect — not replace — the disclosures in the
Investment Memorandum. It does not constitute a public offer, prospectus,
financial advice, tax advice, or legal advice in any jurisdiction. All
Founding Members must sign an NDA and Indemnity Form before a bid is
accepted, and all final terms are subject to signed legal documentation and
compliance with the South African Companies Act, 71 of 2008.

---

## Pushing to GitHub

This folder is already a git repository with an initial commit. From your own
machine (with `git` and the [GitHub CLI](https://cli.github.com/) or your own
GitHub credentials configured):

```bash
cd gravitas-founding-members

# Option A — GitHub CLI (creates the repo and pushes in one step)
gh repo create gravitas-founding-members --private --source=. --remote=origin --push

# Option B — manual
# 1. Create an empty repo on github.com named "gravitas-founding-members"
# 2. Then:
git remote add origin https://github.com/<your-username>/gravitas-founding-members.git
git branch -M main
git push -u origin main
```

© 2026 Gravitas Industries (Pty) Ltd.
