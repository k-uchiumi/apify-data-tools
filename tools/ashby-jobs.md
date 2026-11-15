# Ashby Jobs Scraper: Job Board API Export

**Run it on Apify:** https://apify.com/ucchiii/ashby-jobs · **Price:** $1.30 per 1,000 jobs · free demo with empty input

**For recruiters, sales-intelligence teams, job-board builders and analysts who need the open jobs of startups and scale-ups that hire through Ashby.**
Paste Ashby job board names or `jobs.ashbyhq.com` URLs and get **one clean row per job**, with the pay range when the company publishes one. **$1.30 per 1,000 jobs**, you pay only for jobs returned.

**How this differs from [Career Site Jobs](https://apify.com/ucchiii/career-site-jobs):** this Actor targets **Ashby only** and takes board names or `jobs.ashbyhq.com` URLs, with no company-name guessing across systems. Career Site Jobs covers six ATS (Greenhouse, Lever, Workable, SmartRecruiters or Recruitee too) and also accepts plain company names.

- **Official public API**: reads the Ashby Posting API (`api.ashbyhq.com/posting-api/job-board/<board>`), the same feed companies use for their own career pages. No login, no proxies, no blocking.
- **Normalized fields**: title, company, location(s), remote / workplace type, team, department, employment type, job and apply URL, published date, plain-text description, salary when the posting publishes a range.
- **Filters**: title keywords, location, remote only, posted within N days, max jobs per board.
- **Same field names as [Career Site Jobs](https://apify.com/ucchiii/career-site-jobs)**, so datasets can be combined.
- **Polite, fixed limits**: boards are fetched one at a time with a 1 second pause, at most 100 boards per run, with timeouts and retries with backoff on 429 / 5xx. These limits cannot be changed from the input.
- **Free demo**: leave the input empty and the Actor returns a few sample jobs from two public Ashby boards without charging.

Need Greenhouse, Lever, Workable, SmartRecruiters or Recruitee too? Use [Career Site Jobs](https://apify.com/ucchiii/career-site-jobs) for multi-ATS needs.

## Input
```json
{
  "companies": ["ramp", "https://jobs.ashbyhq.com/notion", "ashby:linear"],
  "keywords": ["engineer", "data"],
  "locations": ["London", "Remote"],
  "remoteOnly": false,
  "postedWithinDays": 30,
  "maxJobsPerCompany": 100
}
```
`companies` accepts, one per line (up to 100 per run):
- a board name, e.g. `ramp` (the part after `jobs.ashbyhq.com/`)
- `ashby:name`
- a URL: `jobs.ashbyhq.com/<name>` or a posting URL on that host

Other options: `includeDescription` (default on), `includeDescriptionHtml` (default off).

## Output (one item per job)
```json
{
  "company": "Ramp", "ats": "ashby", "boardSlug": "ramp", "jobId": "0b1c2d3e-…",
  "title": "Senior Data Scientist", "department": "Data", "team": "Insights",
  "location": "New York", "locations": ["New York", "Remote - US"], "country": "United States", "countryCode": "US",
  "remote": false, "workplaceType": "hybrid", "employmentType": "FULL_TIME", "employmentTypeRaw": "FullTime",
  "postedAt": "2026-10-01T15:04:05Z", "updatedAt": null,
  "url": "https://jobs.ashbyhq.com/ramp/0b1c2d3e-…", "applyUrl": "https://jobs.ashbyhq.com/ramp/0b1c2d3e-…/application",
  "salaryMin": 150000, "salaryMax": 200000, "salaryCurrency": "USD", "salaryInterval": "year",
  "salaryText": "$150K - $200K",
  "descriptionText": "…", "descriptionTruncated": false
}
```
- `company` is derived from the board name (the API does not return a company name).
- `employmentType` is normalized (`FULL_TIME`, `PART_TIME`, `CONTRACT`, `TEMPORARY`, `INTERN`, `OTHER`); the original value is in `employmentTypeRaw`.
- Salary fields are filled only when the posting publishes a pay range; otherwise `null`.
- Fields Ashby does not provide are `null`. `descriptionText` and `descriptionHtml` are each cut at 30,000 characters (`descriptionTruncated` is then `true`).
- Postings that Ashby marks as not listed are skipped.
- A per-board summary (found / not found, jobs on board, jobs returned) is saved to the key-value store as `SUMMARY`.

## Pricing
Pay per event: **$0.0013 per job returned** ($1.30 per 1,000). Filters are applied before charging. The empty-input demo is free.
On the Apify Free plan, the $5 of monthly platform credit covers about **3,800 jobs** a month — enough to try it on real data.
Set a max charge per run in the run options to cap spend.

## Use cases
- Hiring-signal feeds: which companies are hiring for which roles
- Niche job boards and aggregators
- Competitor hiring trackers: run weekly on a fixed list of boards, filter by `keywords`
- Labour-market and compensation research (pay ranges where published)

## Notes
- Data comes only from the public Ashby Posting API. No personal data is collected.
- A board name that does not exist is reported as `not_found` in `SUMMARY` and is not charged.
- Boards beyond the 100 per run limit are listed as `skipped_limit` in `SUMMARY`; run again with the rest.
- Not affiliated with Ashby.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Remote Jobs API](https://apify.com/ucchiii/himalayas-jobs-api) — remote job listings from the official Himalayas API, with attribution links
- [Job Listings API](https://apify.com/ucchiii/career-site-jobs) — every open job from Greenhouse, Lever, Ashby, Workable, SmartRecruiters and Recruitee career sites
- [Greenhouse Jobs Scraper](https://apify.com/ucchiii/greenhouse-jobs) — open jobs from any Greenhouse job board via the public Job Board API
- [Lever Jobs Scraper](https://apify.com/ucchiii/lever-jobs) — open postings from any company on Lever via the public Postings API
- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
