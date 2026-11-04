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
