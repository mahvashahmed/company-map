---
description: Research a company for job-prep (trajectory, founders, vision, funding, challenges) by cross-checking multiple independent web sources, then save verified data for the company-map viewer.
argument-hint: [company name]
arguments: company
allowed-tools: WebSearch WebFetch Read Write Edit Bash
---

You are researching **$company** for someone preparing for a job interview or application there. Your output feeds a static viewer (`index.html`) that only trusts data you write to `data/<slug>.json`, so accuracy and honest sourcing matter more than completeness.

## 0. Setup

- Compute the slug: lowercase the company name, replace any run of non-alphanumeric characters with a single `-`, trim leading/trailing `-`. Example: "Notion Labs" → `notion-labs`.
- Get today's date with `date +%Y-%m-%d` (bash) — use it as `researchedAt`.

## 1. Research broadly, don't stop at the first hit

Use WebSearch and WebFetch across a spread of source types — don't rely on a single site, and don't lean on tertiary aggregators when a primary source exists.

- **Primary (start here; require at least one per section whenever the company has a public site)**: the company's own site (About/Press/Newsroom), SEC/regulatory filings, official press releases, founders' own interviews or talks.
- **Independent journalism**: Reuters, Bloomberg, TechCrunch, The Information, trade press — must have a named author/publication and a visible date. Prefer pieces from the last 12–18 months, especially for funding figures, stats, and "current challenges."
- **Data providers** (for hard numbers like funding rounds — use to cross-check, not as your only citation): Crunchbase, PitchBook, Tracxn. Their public pages are often paywalled teasers; if you can't see the underlying figure, don't cite it as if you did.
- **Professional/org**: LinkedIn company and founder pages.
- **Culture/jobs**: Glassdoor, AmbitionBox, Blind, the company's actual open job listings — for the Job Insights section. Don't rely on only one of these; employee-review sites skew toward disgruntled reviewers, so cross-check tone against at least one other source when you can.

**Never cite:**
- **Wikipedia.** It's a tertiary aggregator, not an independent source. If a Wikipedia article points to something useful, open the footnote and cite the original source instead — never Wikipedia itself.
- **Unauthored/undated SEO or listicle content** (generic "history of X" or "X's competitors" round-up blogs with no visible byline or publish date). If you can't tell who wrote it or when, don't cite it.
- **Vendor or competitor blogs writing about the subject company's weaknesses** (e.g. a chargeback-management vendor blogging about a payments company's "problems"). They have a commercial incentive to spin the narrative — fine as a lead to chase elsewhere, never as the cited source.

Gather enough raw material to cover: overview/description, founding story & vision, a breakdown of distinct revenue streams (how the company actually makes money), founders' backgrounds, a timeline of major milestones, funding rounds, products/services, problems the company solves, current challenges/risks, and job/team insights.

## 2. Verify before writing anything down

For every individual fact (a date, a number, a claim):

- **`confidence: "verified"`** — either (a) confirmed by 2+ independent sources that agree, or (b) sourced directly from the company's own official materials or a regulatory filing.
- **`confidence: "reported"`** — only one secondary source mentions it and you couldn't independently confirm it. Still include it (it's useful for interview prep) but it must be labeled this way — never silently upgrade it.
- **Two sources only count as "independent" if neither is derived from the other.** If both articles clearly repeat the same original press release, filing, or each other's numbers, that's one source, not two — it does not clear the "verified" bar by itself.
- **Flag stale numbers.** For funding, valuation, employee counts, and other point-in-time stats: if the freshest source you found is older than ~18 months, mark that fact `"reported"` even if multiple old sources agree — it may no longer be current.
- **Disclose conflicts, don't silently resolve them.** If sources disagree on a fact (different valuations, founding dates, headcounts), don't just pick one — either note the discrepancy in the text or mark the section `"reported"`.
- **Do not fabricate or estimate.** If a section has no reliable information after a genuine search, leave it empty (`[]`, `null`, or omit optional fields) rather than guessing. An honest "not available" beats a plausible-sounding invention.
- Confidence is set **per section** (not per field) in the schema below — if a section mixes verified and single-source facts, mark the section `"reported"` and use judgement about which sub-facts to keep.
- Every section must carry the actual URLs you used for it in `sources`. Don't list a source you didn't actually fetch/read.

## 2.5 Write in plain language

This viewer is for someone prepping for an interview, not a finance or industry professional. Write every description, background, event, and detail field in **plain, everyday English**:

- Avoid unexplained jargon and abbreviations — "merchant of record," "interchange fee," "tender offer," "Series H," "YoY," "MoR," "D2C/B2B," "WFH," "SEC 8-K filing," etc. either need a short plain-English gloss inline (e.g. "a tender offer — a sale where employees and early investors get to cash out some shares") or should be replaced outright with the plain description.
- Prefer the everyday word over the technical one: "sold to Google" over "acquired by," "raised money from investors" over "closed a funding round," "lays off" over "reduces headcount."
- It's fine to name a proper noun (a product name, a round label like "Series C") as long as the surrounding sentence explains what it actually means in context — don't assume the reader already knows.
- This applies to every text field: `description`, `businessModel`, `problemsSolved`, founder `background`, timeline `event`, funding `round`/`amount`, challenge `details`, and jobInsights `text`. It does not require dumbing down facts or numbers — just explaining them in ordinary words.

## 3. Write `data/<slug>.json`

Create the `data/` directory if it doesn't exist. Match this schema exactly (omit an `items`/field only where noted, but keep the key names):

```json
{
  "company": "Display Name",
  "slug": "display-name",
  "researchedAt": "YYYY-MM-DD",
  "overview": {
    "description": "2-4 sentence description of what the company does",
    "founded": "Year or date",
    "headquarters": "City, Country",
    "industry": "Industry / category",
    "parent": "Parent company or null",
    "currentLeader": { "name": "...", "role": "CEO" },
    "vision": "Short mission/vision statement, quoted from the company if possible, or null",
    "problemsSolved": ["Problem 1", "Problem 2"],
    "stats": { "monthlyUsers": "...", "valuation": "...", "employees": "...", "revenue": "..." },
    "confidence": "verified | reported",
    "sources": [{ "url": "https://...", "label": "Short source name" }]
  },
  "businessModel": {
    "summary": "1-2 sentence framing of the overall revenue model (e.g. 'Primarily transaction-based, layered with subscription products')",
    "items": [{ "stream": "Name of the revenue stream", "detail": "1-2 sentences on how it works and how big/important it is, if known" }],
    "confidence": "verified | reported",
    "sources": [{ "url": "https://...", "label": "Short source name" }]
  },
  "founders": {
    "items": [{ "name": "...", "role": "Co-founder / CEO", "background": "1-2 sentences" }],
    "confidence": "verified | reported",
    "sources": [{ "url": "...", "label": "..." }]
  },
  "timeline": {
    "items": [{ "year": "2015", "event": "What happened" }],
    "confidence": "verified | reported",
    "sources": [...]
  },
  "funding": {
    "items": [{ "round": "Series B", "year": "2019", "amount": "$50M", "valuation": "$500M", "investor": "Lead investor(s)" }],
    "confidence": "verified | reported",
    "sources": [...]
  },
  "products": {
    "items": ["Product/service name", "..."],
    "confidence": "verified | reported",
    "sources": [...]
  },
  "challenges": {
    "items": [{ "challenge": "Short title", "details": "1-3 sentences, ideally tied to a recent (last 12-18mo) source" }],
    "confidence": "verified | reported",
    "sources": [...]
  },
  "jobInsights": {
    "items": [{ "category": "Whatever role/department the research actually surfaced", "text": "1-3 sentences on what that team looks for, interview themes, day-to-day, or culture notes" }],
    "confidence": "verified | reported",
    "sources": [...]
  }
}
```

**`jobInsights.items` categories are not fixed.** Don't default to a tech-company taxonomy (Product/Engineering/Data-ML/Trust&Safety) unless the company is actually a tech company with those teams. Base the categories entirely on what you find for *this* company's actual employees and roles — e.g. a therapy platform might get `"Therapists / Clinical"` and `"Growth & Ops"`; a retailer might get `"Store Ops"` and `"Merchandising"`; a bank might get `"Risk & Compliance"` and `"Relationship Managers"`. Pull this from Glassdoor/AmbitionBox/Blind reviews, the careers page's actual open roles, and any interview-experience write-ups — reflect the departments that are real for this employer, however many or few that turns out to be (could be one category, could be five).

**`businessModel.items` should be genuinely distinct revenue streams**, not restatements of the same stream in different words — e.g. Stripe's real streams are payment-processing fees, subscription/usage products (Billing, Radar, Treasury), and financing fees, not "revenue" split three ways. If the company only has one real revenue stream (common for early-stage or single-product companies), a single honest item is correct — don't pad it out to look more complete than it is.

Leave a section's `items` as `[]` (and `sources` as `[]`) if nothing reliable was found — the viewer renders a clean "not available" state for that.

## 4. Update the manifest

Read `data/manifest.json` (create it as `[]` if missing). Upsert an entry for this company by `slug`:

```json
{ "slug": "display-name", "name": "Display Name", "researchedAt": "YYYY-MM-DD" }
```

Write the file back as a JSON array, sorted by `name`.

## 5. Report back

Print a short summary table: each section, its confidence level, and how many independent sources backed it. Flag any section that came back empty. Remind the user to serve the folder locally (e.g. `python3 -m http.server 8000`) and open `index.html` to view the result — opening the file directly (`file://`) will not be able to load the JSON.
