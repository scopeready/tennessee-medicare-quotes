# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Static marketing/lead-generation site for ECOS Medicare Solutions (agent: Darin Weidauer, NPN 18580338) serving Tennessee, built for https://www.tennesseemedicarequotes.com. There is no framework, package manager, build step, test suite, or linter in this repo — it holds hand-editable HTML plus shared assets, generated from a small set of Python build scripts (kept outside this repo) that share a template with the Nevada, Colorado, Arizona and Georgia ECOS sites.

## History

Built from scratch in September 2026 (unlike the Colorado repo, there was no prior SiteGround site to migrate) as the fifth site in the ECOS Medicare Solutions network, following the same template and structure already established by the Nevada/Colorado sites: same design system (`style.css`/`analytics.js`/`favicon.svg` are the literal state-agnostic files from those repos), same page shape (JSON-LD, skip-link, byline, lead-capture forms), same core page set plus statewide city/county coverage.

**Two deliberate differences from the Colorado pattern, both flagged to the user and worth knowing before you "make it consistent" with the other sites:**

1. **Phone number.** The site currently uses `(702) 706-6564` — Nevada's number — as an explicit temporary placeholder, at the user's instruction ("use my 702-706-6564 for now"). This is a real business decision to revisit before the site is taken seriously as a distinct local presence: a phone number shared with another state's site is a duplicate-NAP (Name-Address-Phone) signal that can hurt local SEO for both sites. Get the real Tennessee number from the user and do a site-wide replace (grep every `.html` file for `702) 706-6564` and `+17027066564`) before this goes fully live long-term.
2. **Carrier list is 8, not 10.** Every other ECOS state site advertises "10 carriers" including Kaiser Permanente, Select Health and Anthem Blue Cross Blue Shield. None of those three are known to operate Medicare plans in Tennessee (Kaiser and Select Health don't sell there at all; Tennessee's Blue plan is the independent BlueCross BlueShield of Tennessee, not Anthem). Rather than copy a likely-wrong carrier list into a regulated insurance solicitation, this site lists 8 carriers Darin is confident are real, active options in Tennessee: Humana, UnitedHealthcare, Aetna, BlueCross BlueShield of Tennessee, Cigna Healthspring, Wellcare, Devoted Health, Mutual of Omaha. **Verify this list against Darin's actual Tennessee carrier appointments before launch** and correct it (and the "8 carriers" copy throughout — search for `8 carriers`, `8 Tennessee carriers`, `8 top`, `CARRIERS =`) if it's wrong in either direction.

No DSNP / C-SNP / I-SNP pages exist, and there is no "Medigap birthday rule" page — Tennessee is not known to have a state-specific Medigap birthday/anniversary underwriting rule. Don't add one without a citation to a specific Tennessee statute or DOI bulletin.

## Deployment

- Intended for **Vercel**, auto-deploying from GitHub. `vercel.json` sets `cleanUrls: false` and `trailingSlash: false` — pages are served at their literal `.html` filenames. Do not switch this to clean URLs without rewriting every internal href and the sitemap.
- Internal links are **relative, with the `.html` suffix**. Canonical URLs in each page's `<head>` are absolute, on the `www.` subdomain: `https://www.tennesseemedicarequotes.com/...` (unlike the Colorado/Nevada sites, which are bare-domain — this one was specified with `www.` explicitly).

## Site structure

All current pages are flat files at the repo root.

- **Core pages**: `index.html`, `about.html`, `contact.html`, `enroll.html`, `faq.html`, `medicare-basics.html`, `medicare-advantage-tennessee.html`, `medigap-plan-g-vs-plan-n.html`, `medicare-costs.html`, `part-d.html`, `turning-65.html`, `medicare-penalty-calculator.html`, `irmaa.html`, `medicaid.html` (TennCare), `veterans-medicare.html`, `areas-we-serve.html`, `thank-you.html`, `privacy.html`, `terms.html`, `404.html`.
- **City pages** (26): `medicare-<city>-tn.html` — Memphis, Jackson, Bartlett, Germantown, Collierville (West TN); Nashville, Murfreesboro, Franklin, Clarksville, Hendersonville, Smyrna, Brentwood, Columbia, Gallatin, Cookeville, Lebanon, Spring Hill (Middle TN); Knoxville, Chattanooga, Johnson City, Kingsport, Bristol, Cleveland, Maryville, Morristown, Oak Ridge (East TN).
- **All 95 counties** are covered on `areas-we-serve.html`, grouped into Tennessee's three Grand Divisions (West/Middle/East — the same divisions on the state flag's three stars) and 8 sub-regions. Every county links to its nearest built city page rather than getting its own page — deliberately, from the start (a lesson learned building the Colorado site, where county tags shipped non-clickable and had to be fixed after launch). If you add a county-specific page later, update `COUNTY_TO_CITY_SLUG`-equivalent logic and the county's tag target together; don't leave a county pointing at a page that no longer represents it.
- Shared assets at the root: `style.css`, `analytics.js`, `favicon.svg`, `og-image.png` (Tennessee-branded, 1200×630), `logo.jpg`, `headshot.jpg`, and 5 lead-magnet PDFs — all reused as-is from the Colorado repo since they are genuinely state-agnostic (ECOS wordmark logo, Darin's headshot, and generic "Retire With Confidence" style guides with no state-specific content).

## Every page's shape

Same as the Colorado site: top bar (phone + NPN), sticky header (logo + nav + call CTA), hero section, JSON-LD (`WebPage`, `InsuranceAgency`+`Person` graph with `sameAs` linking every other ECOS state site, `BreadcrumbList`, `FAQPage` where applicable), a lead-capture form posting to Web3Forms, bottom CTA, shared footer. Educational and city pages carry a "Written and reviewed by Darin Weidauer" byline. Copy this shape from an existing page when adding a new one.

## Contact form

Same Web3Forms + TCPA-consent pattern as every other ECOS site: `botcheck` honeypot, required `consent` checkbox with full consent text in a hidden field, JS-stamped `consent_timestamp`. Don't weaken this.

## Key conventions

- **Pages are self-contained** — header, nav, footer, disclaimers and JSON-LD are duplicated in every file. A site-wide change (phone, NPN, carrier list, footer links, disclaimer wording) needs a grep-and-verify pass across every `.html` file, not just one.
- **SEO/AI-discovery files must stay in sync with the page set** — update `sitemap.xml`, `llms.txt`, `llms-full.txt` whenever a page is added, renamed or removed.
- **Content is written fresh per state, not copy-pasted from a sibling site with find-and-replace.** The 2026 CMS dollar figures (Part A/B/D, IRMAA table) are identical across every ECOS state site by design — that's correct, they're the same federal numbers. But the surrounding prose (intros, examples, FAQ phrasing) was deliberately written new for Tennessee rather than reused verbatim from Colorado/Nevada, to avoid a near-duplicate-content footprint across the network. Keep that discipline when extending this site or building the next state.

## Compliance — do not weaken

This is a Medicare marketing site subject to CMS/TPMO rules:

- Every page carries the TPMO disclaimer. Preserve this wording site-wide.
- Dollar figures (Part B premium, deductibles, Part D cap, IRMAA brackets) are the same verified 2026 CMS figures used across the ECOS network — don't invent or "update" them without the user supplying verified figures for the new plan year.
- 1-800-MEDICARE is referenced as the official fallback.
- Tennessee's Medicaid program is **TennCare**, referenced on `medicaid.html`. Don't rename it or attribute another state's program mechanics to it.
- **The carrier list and phone number in this repo are known-provisional — see History above.** Don't treat either as verified simply because it matches the pattern of the other sites.

## Local preview

```bash
python3 -m http.server 8000   # then open http://localhost:8000/index.html — filenames are the real routes
```
