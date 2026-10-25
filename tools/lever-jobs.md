# Lever Jobs Scraper: Postings API Export

**Run it on Apify:** https://apify.com/ucchiii/lever-jobs · **Price:** $1.00 per 1,000 jobs · free demo with empty input

**For recruiters, sales-intelligence teams, job-board builders and analysts who need the open jobs of companies that hire through Lever.**
Paste Lever company slugs or `jobs.lever.co` URLs and get **one clean row per job**. **$1.00 per 1,000 jobs**, you pay only for jobs returned.

**How this differs from [Career Site Jobs](https://apify.com/ucchiii/career-site-jobs):** this Actor targets **Lever only** and takes company slugs or `jobs.lever.co` URLs, with no company-name guessing across systems. Career Site Jobs covers six ATS (Greenhouse, Ashby, Workable, SmartRecruiters or Recruitee too) and also accepts plain company names.

- **Official public API**: reads the Lever Postings API (`api.lever.co`, US and EU hosts), the same feed companies use for their own career pages. No login, no proxies, no blocking.
- **Normalized fields**: title, company, location(s), remote / workplace type, team, department, commitment, job and apply URL, posted date, plain-text description, salary when the posting publishes a range.
- **Filters**: title keywords, location, remote only, posted within N days, max jobs per company.
- **Same field names as [Career Site Jobs](https://apify.com/ucchiii/career-site-jobs)**, so datasets can be combined.
- **Polite**: companies are fetched one at a time, with timeouts and retries with backoff on 429 / 5xx.
- **Free demo**: leave the input empty and the Actor returns a few sample jobs from two public Lever sites without charging.

Need Greenhouse, Ashby, Workable, SmartRecruiters or Recruitee too? Use [Career Site Jobs](https://apify.com/ucchiii/career-site-jobs) for multi-ATS needs.

## Input
```json
{
  "companies": ["palantir", "https://jobs.lever.co/spotify", "lever:netflix"],
  "keywords": ["engineer", "data"],
  "locations": ["London", "Remote"],
  "remoteOnly": false,
  "postedWithinDays": 30,
  "maxJobsPerCompany": 100
}
```
`companies` accepts, one per line:
- a company slug, e.g. `palantir` (the part after `jobs.lever.co/`). A bare slug is looked up on the US host first, then the EU host.
- `lever:slug`
- a URL: `jobs.lever.co/<slug>`, `jobs.eu.lever.co/<slug>`, or a posting URL

Other options: `includeDescription` (default on), `includeDescriptionHtml` (default off).

## Output (one item per job)
```json
{
  "company": "Spotify", "ats": "lever", "boardSlug": "spotify", "jobId": "0b1c2d3e-…",
  "title": "Senior Data Scientist", "department": "Data", "team": "Insights",
  "location": "London", "locations": ["London", "Stockholm"], "country": "GB", "countryCode": "GB",
  "remote": false, "workplaceType": "hybrid", "employmentType": "FULL_TIME", "employmentTypeRaw": "Permanent",
  "postedAt": "2026-10-01T15:04:05Z", "updatedAt": null,
  "url": "https://jobs.lever.co/spotify/0b1c2d3e-…", "applyUrl": "https://jobs.lever.co/spotify/0b1c2d3e-…/apply",
  "salaryMin": 90000, "salaryMax": 120000, "salaryCurrency": "GBP", "salaryInterval": "year",
  "descriptionText": "…", "descriptionTruncated": false
}
```
- `company` is derived from the slug (Lever does not return a company name).
- `employmentType` is normalized (`FULL_TIME`, `PART_TIME`, `CONTRACT`, `TEMPORARY`, `INTERN`, `OTHER`); the original value is in `employmentTypeRaw`.
- Salary fields are filled only when the posting publishes a range; otherwise `null`.
- Fields Lever does not provide are `null`. `descriptionText` and `descriptionHtml` are each cut at 30,000 characters (`descriptionTruncated` is then `true`).
- A per-company summary (found / not found, jobs on board, jobs returned) is saved to the key-value store as `SUMMARY`.

## Pricing
Pay per event: **$0.001 per job returned** ($1.00 per 1,000). Filters are applied before charging. The empty-input demo is free.
Set a max charge per run in the run options to cap spend.

## Use cases
- Hiring-signal feeds: which companies are hiring for which roles
- Niche job boards and aggregators
- Competitor hiring trackers: run weekly on a fixed list of companies, filter by `keywords`
- Labour-market and compensation research

## Notes
- Data comes only from the public Lever Postings API. No personal data is collected.
- A slug that does not exist is reported as `not_found` in `SUMMARY` and is not charged.
- Not affiliated with Lever.

<!-- more-tools:start -->
## More tools from the same developer

- [Himalayas Remote Jobs API](https://apify.com/ucchiii/himalayas-jobs-api) — remote job listings from the official Himalayas API, with attribution links
- [Job Listings API](https://apify.com/ucchiii/career-site-jobs) — every open job from Greenhouse, Lever, Ashby, Workable, SmartRecruiters and Recruitee career sites
- [Greenhouse Jobs Scraper](https://apify.com/ucchiii/greenhouse-jobs) — open jobs from any Greenhouse job board via the public Job Board API
- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
