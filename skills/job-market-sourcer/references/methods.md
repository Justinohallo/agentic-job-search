# Sourcing methods that work (verified 2026-11-04)

## LinkedIn guest endpoints (no login, parseable HTML)

- Search: `https://www.linkedin.com/jobs-guest/jobs/api/seeMoreJobPostings/search?keywords=<q>&location=<loc>&f_TPR=r1209600&f_WT=2&f_JT=C&start=0`
  - `f_WT=2` remote · `f_JT=C` contract (loose: most hits are full-time) · `f_TPR` window in seconds (r1209600 = 14 days) · page with `start`
- Detail: `https://www.linkedin.com/jobs-guest/jobs/api/jobPosting/<id>`. Read "Employment type" here; it's the only reliable contract check.
- Unofficial: can rate-limit or vanish. Signed-in LinkedIn UI hides job ids, so use these for links.

## Ashby boards

- `https://api.ashbyhq.com/posting-api/job-board/<company>` returns every posting as JSON.
- Form schema without a browser: POST `https://jobs.ashbyhq.com/api/non-user-graphql?op=ApiJobPosting` with a `jobPosting { applicationForm { ... } }` query.

## Agencies

- Western Canada agencies fill from their databases before posting, so registration matters more than scanning.
- Most US staffing roles are W2 / US-authorized only. For a Canadian, the US route is remote platforms (Toptal, Braintrust, A.Team, Kake, Lazer, Terminal) or C2C.

## People at a company

- LinkedIn people search filtered to the company and to 1st/2nd-degree connections, read-only.
- Engineering blog authors, conference speakers and GitHub org members name the team leads.

## LinkedIn notifications page (verified 2026-11-09)

The signed-in notifications page is the densest list of what LinkedIn is pushing. Each job card ("<alert>: new opportunities in <place>", "See 10 jobs similar to one you recently viewed", "You're a top applicant for…") links to a search URL whose `originToLandingJobPostings` parameter lists the posting ids, up to 10 per card. The signed-in UI hides ids everywhere else, so this is the one place to get them in bulk.

1. Announce first, then open `https://www.linkedin.com/notifications/` in the candidate's signed-in Chrome (Control_Chrome `open_url`). Read only: no clicks.
2. Run this in the tab (`execute_javascript`); it returns one row per card with the alert name, the card origin and the ids:

```js
(() => { const seen = new Set(), out = [];
  document.querySelectorAll('a[href*="originToLandingJobPostings"]').forEach(a => {
    if (seen.has(a.href)) return; seen.add(a.href);
    let c = a; for (let i = 0; i < 6 && c.parentElement; i++) { c = c.parentElement; if ((c.innerText || '').length > 60) break; }
    const u = new URL(a.href);
    out.push({ alert: u.searchParams.get('keywords'), origin: u.searchParams.get('origin'),
      ids: u.searchParams.get('originToLandingJobPostings').split(','),
      text: (c.innerText || '').replace(/\s+/g, ' ').trim().slice(0, 200) });
  });
  return JSON.stringify(out); })()
```

3. Close the tab. Union the ids, drop any already in `job-leads/seen-ids.txt`, and read each remaining id with the guest detail endpoint above (title, company, location, posted age, employment type, description with pay).
4. Append every id read to `job-leads/seen-ids.txt` (one per line, `<id> <date> <company>`), so the next run dedupes by id rather than by company name.

Limits: the page shows only the ~10 most recent notifications (scrolling loads no more on 2026-11-09), so it has to be read daily or the older cards are lost. The cards mirror the saved alerts, so alert tuning still decides the quality. `origin` tells cards apart: `SEMANTIC_SEARCH_JOB_ALERT_IN_APP_NOTIFICATION` (saved alert), `BYV_SIMILAR_JOBS_IN_APP_NOTIFICATION` (similar to a posting the candidate viewed; often the best-targeted cards), `JobSearchOrigin_TOP_APPLICANT_NOTIFICATION_LANDING` (Premium top-applicant list; the URL carries only one id).

## Employer feeds: a company's own ATS (verified 2026-11-10)

LinkedIn search ranks across a whole region, so a short list of anchor employers gets drowned out, and some roles never reach LinkedIn search at all. Two cheap fixes, in order:

1. **LinkedIn company filter.** Add `f_C=<id>,<id>,...` to the guest search. The numeric id is in `<meta name="companyId">` on any public job page (`https://ca.linkedin.com/jobs/view/<jobId>`); company pages are behind the login wall. One query covers 20 companies and keeps LinkedIn ids, so the seen-ids ledger works unchanged. The guest endpoints return HTTP 429 after roughly 8 rapid requests: back off and keep it to two keywords per run. Keyword matches can be in the body, so don't title-filter these hits.
2. **The company's own ATS feed.** Identify it once from an apply link, then pull JSON:

| ATS | How to spot it | Public read |
|---|---|---|
| Greenhouse | `boards.greenhouse.io/<token>` | `GET https://boards-api.greenhouse.io/v1/boards/<token>/jobs` |
| Ashby | `jobs.ashbyhq.com/<token>` | `GET https://api.ashbyhq.com/posting-api/job-board/<token>` |
| Lever | `jobs.lever.co/<token>` (token can contain a dot, e.g. `brand.com`) | `GET https://api.lever.co/v0/postings/<token>?mode=json` |
| Workday | `<tenant>.wd<N>.myworkdayjobs.com/<site>` | `POST https://<host>/wday/cxs/<tenant>/<site>/jobs` with `{"limit":20,"offset":0,"searchText":"<city>"}` |
| Avature | `/careers/JobDetail/<slug>/<id>` URLs | `GET <base>/en_US/careers/SearchJobs/<keyword>?listFilterMode=1` (HTML; no location, read each detail page) |
| Amazon | amazon.jobs | `GET https://www.amazon.jobs/en/search.json?latitude=..&longitude=..&radius=24km&category[]=software-development` |
| SuccessFactors | `/job/<City>-<Title>/<id>` URLs | `/services/rss/job/?keywords=` exists but may ignore keywords or sit behind Cloudflare; fall back to route 1 |

Prefix non-LinkedIn ids by source (`gh:`, `ashby:`, `lever:`, `wd:<tenant>:`, `avature:<site>:`, `amzn:`) so one seen-ids ledger dedupes both routes. Generic board tokens can belong to a different company with the same name: check the board's locations before trusting it. Each source fails differently, so one dead source logs an error and never stops the sweep.

What the first live run showed: the company filter found most anchor roles, and the direct feeds added roles LinkedIn search hadn't surfaced (a big-tech "web frameworks" role and two full-stack roles on a Workday tenant). That's the case for keeping both routes.
