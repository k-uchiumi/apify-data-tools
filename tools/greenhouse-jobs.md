# Greenhouse Jobs Scraper: Job Board API Export

**Run it on Apify:** https://apify.com/ucchiii/greenhouse-jobs · **Price:** $1.00 per 1,000 jobs · free demo with empty input

**For recruiters, sales-intelligence teams, job-board builders and analysts who need the open jobs of companies that hire through Greenhouse.**
Paste Greenhouse board tokens or board URLs and get **one clean row per job**. **$1.00 per 1,000 jobs**, you pay only for jobs returned.

**How this differs from [Career Site Jobs](https://apify.com/ucchiii/career-site-jobs):** this Actor targets **Greenhouse only** and takes board tokens or Greenhouse board URLs, with no company-name guessing across systems. Career Site Jobs covers six ATS (Lever, Ashby, Workable, SmartRecruiters or Recruitee too) and also accepts plain company names.

- **Official public API**: reads the Greenhouse Job Board API (`boards-api.greenhouse.io`), the same feed companies use for their own career pages. No login, no proxies, no blocking.
- **Normalized fields**: title, company, location(s), remote flag, department, job URL, posted and updated dates, plain-text description, salary when the board publishes pay ranges.
- **Filters**: title keywords, location, remote only, posted within N days, max jobs per board.
- **Same field names as [Career Site Jobs](https://apify.com/ucchiii/career-site-jobs)**, so datasets can be combined.
- **Polite**: boards are fetched one at a time, with timeouts and retries with backoff on 429 / 5xx.
- **Free demo**: leave the input empty and the Actor returns a few sample jobs from two public boards without charging.

Need Lever, Ashby, Workable, SmartRecruiters or Recruitee too? Use [Career Site Jobs](https://apify.com/ucchiii/career-site-jobs) for multi-ATS needs.

## Input
```json
{
  "boards": ["airbnb", "https://boards.greenhouse.io/stripe", "greenhouse:figma"],
  "keywords": ["engineer", "data"],
  "locations": ["United States", "Remote"],
  "remoteOnly": false,
  "postedWithinDays": 30,
  "maxJobsPerBoard": 100
}
```
`boards` accepts, one per line:
- a board token, e.g. `airbnb` (the part after `boards.greenhouse.io/`)
- `greenhouse:token`
- a board or job URL: `boards.greenhouse.io/<token>`, `job-boards.greenhouse.io/<token>`, `boards.greenhouse.io/embed/job_board?for=<token>`

Other options: `includeDescription` (default on), `includeDescriptionHtml` (default off).

## Output (one item per job)
```json
{
  "company": "Airbnb", "ats": "greenhouse", "boardSlug": "airbnb", "jobId": "1234567",
  "title": "Senior Software Engineer, Payments", "department": "Engineering", "team": null,
  "location": "United States", "locations": ["United States"], "country": null, "countryCode": null,
  "remote": null, "workplaceType": null, "employmentType": null,
  "postedAt": "2026-10-01T15:04:05Z", "updatedAt": "2026-10-20T09:00:00Z",
  "url": "https://boards.greenhouse.io/airbnb/jobs/1234567", "applyUrl": "https://boards.greenhouse.io/airbnb/jobs/1234567",
  "salaryMin": 180000, "salaryMax": 250000, "salaryCurrency": "USD", "salaryText": "Base pay",
  "descriptionText": "…", "descriptionTruncated": false
}
```
- `remote` is `true` when the location text contains "remote", otherwise `null` (Greenhouse has no remote field).
- Salary fields are filled only when the board publishes pay ranges; otherwise `null`.
- Fields Greenhouse does not provide are `null`. `descriptionText` and `descriptionHtml` are each cut at 30,000 characters (`descriptionTruncated` is then `true`).
- A per-board summary (found / not found, jobs on board, jobs returned) is saved to the key-value store as `SUMMARY`.

## Pricing
Pay per event: **$0.001 per job returned** ($1.00 per 1,000). Filters are applied before charging. The empty-input demo is free.
Set a max charge per run in the run options to cap spend.

## Use cases
- Hiring-signal feeds: which companies are hiring for which roles
- Niche job boards and aggregators
- Competitor hiring trackers: run weekly on a fixed list of boards, filter by `keywords`
- Labour-market and compensation research

## Notes
- Data comes only from the public Greenhouse Job Board API. No personal data is collected.
- A board token that does not exist is reported as `not_found` in `SUMMARY` and is not charged.
- Not affiliated with Greenhouse.

<!-- more-tools:start -->
## More tools from the same developer

- [Himalayas Remote Jobs API](https://apify.com/ucchiii/himalayas-jobs-api) — remote job listings from the official Himalayas API, with attribution links
- [Job Listings API](https://apify.com/ucchiii/career-site-jobs) — every open job from Greenhouse, Lever, Ashby, Workable, SmartRecruiters and Recruitee career sites
- [Lever Jobs Scraper](https://apify.com/ucchiii/lever-jobs) — open postings from any company on Lever via the public Postings API
- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
