# Job Listings API: Greenhouse, Lever, Ashby Career Sites

**Run it on Apify:** https://apify.com/ucchiii/career-site-jobs · **Price:** $1.00 per 1,000 jobs · free demo with empty input

**Job listings from company career sites: every open job in one table**

| company | ats | title | location | workplaceType | salary (min–max) | postedAt |
|---|---|---|---|---|---|---|
| Ramp | ashby | Security Engineer, Cloud | New York, NY (HQ) | hybrid | 211,400–290,600 USD / year | 2026-04-07 |
| Ramp | ashby | Mobile Engineer, Android | New York, NY (HQ) | hybrid | 189,000–330,000 USD / year | 2025-07-31 |
| Airbnb | greenhouse | AirCover Specialist, Claims Experience | Canada | | | 2026-10-28 |
| Hugging Face | workable | Open-Source Machine Learning Engineer - EMEA Remote | Paris, Île-de-France, France | remote | | 2026-05-29 |

*(real output of test runs on 2026-11-04, shortened to 7 of the columns; empty cells are fields the company does not publish)*

**For recruiters, sales-intelligence teams and job-board builders who need open jobs from many companies** without
writing one scraper per ATS. Paste career-site URLs or just company names — the Actor finds which ATS each company
uses and returns every open job in **one unified format**.

**Try it:** press **Start** with the prefilled example (Airbnb, Palantir and Ramp, **Max jobs per company** 10). It returns 30 jobs,
charged per job (about 3 cents on the free plan). Clear **Max jobs per company** to get every open job on those three boards:
on 2026-11-04 that was about 690 jobs (Airbnb 212, Palantir 305, Ramp 169) in about a minute (about 69 cents). Clear the **Companies** list and press Start for a free demo:
3 jobs from each of the three companies (9 rows, not charged).

**Price:** from **$0.70 per 1,000 jobs** on Apify's paid plans (the price shown on this page); $1.00 per 1,000 on the free plan.
You pay only for jobs returned after your filters.

- **6 ATS in one Actor**: Greenhouse, Lever (US & EU), Ashby, Workable, SmartRecruiters, Recruitee
- **Official public job board APIs** — the same feeds companies use to power their own career pages. No login, no proxies, no blocking.
- **Unified fields**: title, department, team, location(s), country, remote / workplace type, employment type, posted date, job & apply URL, **salary when the company publishes it** (Ashby, Lever, Recruitee), plain-text description.
- **Filters**: title keywords, locations, remote only, posted within N days, max jobs per company.

## Input
```json
{
  "companies": ["https://boards.greenhouse.io/airbnb", "lever:palantir", "ashby:ramp", "Notion"],
  "titleKeywords": ["engineer", "data"],
  "locations": ["United States", "Remote"],
  "postedWithinDays": 30
}
```
`companies` accepts:
- a career site URL: `boards.greenhouse.io/<co>`, `job-boards.greenhouse.io/<co>`, `jobs.lever.co/<co>`, `jobs.ashbyhq.com/<co>`, `apply.workable.com/<co>`, `jobs.smartrecruiters.com/<Co>`, `<co>.recruitee.com`
- `ats:slug`, e.g. `greenhouse:stripe`
- a company name, e.g. `Notion` — we try every supported ATS (takes a few more seconds)

## Output (one item per job)
```json
{
  "company": "Ramp", "ats": "ashby", "boardSlug": "ramp", "jobId": "…",
  "title": "Senior Software Engineer, Backend", "department": "Engineering", "team": "Platform",
  "location": "New York, NY", "locations": ["New York, NY", "San Francisco, CA"], "country": "USA", "countryCode": "US",
  "remote": false, "workplaceType": "hybrid", "employmentType": "FULL_TIME", "employmentTypeRaw": "FullTime",
  "postedAt": "2026-10-01T15:04:05Z", "updatedAt": null,
  "url": "https://jobs.ashbyhq.com/ramp/…", "applyUrl": "https://jobs.ashbyhq.com/ramp/…/application",
  "salaryMin": 180000, "salaryMax": 250000, "salaryCurrency": "USD", "salaryInterval": "year", "salaryIntervalRaw": "1 YEAR",
  "salaryText": "$180K – $250K", "descriptionText": "…", "descriptionTruncated": false
}
```
Normalized across all ATS: `countryCode` (ISO 3166-1 alpha-2), `employmentType` (`FULL_TIME`, `PART_TIME`, `CONTRACT`,
`TEMPORARY`, `INTERN`, `OTHER`), `workplaceType` (`remote`, `hybrid`, `onsite`), `salaryInterval` (`hour`, `day`, `week`,
`month`, `year`), numeric salaries, trimmed text. The original values are kept in `*Raw` fields and `country`.
When an ATS does not return a company name (Lever, Ashby), `company` is derived from the board name.
Fields an ATS does not provide are `null`. A per-company summary (found / not found, jobs on board, jobs returned)
is saved to the key-value store as `SUMMARY`.

## Pricing
| Event | Free plan | Paid plans (Store discount tiers Bronze / Silver / Gold) |
|---|---|---|
| Job (one job posting returned, after filters) | $1.00 per 1,000 | $0.90 / $0.80 / $0.70 per 1,000 |

The price on this page ("from $0.70") is the Gold tier price. Filters are applied before charging.
On the Apify Free plan, the $5 of monthly platform credit covers about **5,000 jobs** a month — enough to try it on real data.
Set a max charge per run in the run options to cap spend.

## Use cases
- Hiring-signal and sales-intelligence feeds (which companies are hiring for what)
- Job boards and niche job aggregators
- Labour-market and compensation research
- Competitor hiring tracker: run weekly on a fixed company list, filter by `titleKeywords`

## Notes
- Data comes only from public job board APIs that companies publish for their own career pages. No personal data is collected.
- SmartRecruiters does not include descriptions in its list API, so `descriptionText` is empty for SmartRecruiters jobs.
- `descriptionText` and `descriptionHtml` are each cut at 30,000 characters; when that happens the text ends with `…` and `descriptionTruncated` is `true`.
- Not affiliated with any ATS vendor.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Workday Jobs Scraper](https://apify.com/ucchiii/workday-jobs) — open jobs from Workday career sites (myworkdayjobs.com) in the same fields as Career Site Jobs
- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [RSS Feed Reader & Aggregator](https://apify.com/ucchiii/rss-feed-reader) — many RSS / Atom feeds merged into one clean article list
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
