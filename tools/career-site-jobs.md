# Career Site Jobs Scraper: Greenhouse, Lever, Ashby API

**Run it on Apify:** https://apify.com/ucchiii/career-site-jobs · **Price:** $1.00 per 1,000 jobs · free demo with empty input

**For recruiters, sales teams and job-board builders:** collecting open jobs from many company career sites means handling a different ATS format for each one. This Actor returns them all in one unified format (title, location, remote, salary when published) from the public job board APIs. **$1.00 per 1,000 jobs returned.**

Get **every open job from a list of companies in one run**, straight from their career sites.
Paste career-site URLs or just company names — the Actor finds which ATS each company uses and returns
jobs in **one unified format**.

- **6 ATS in one Actor**: Greenhouse, Lever (US & EU), Ashby, Workable, SmartRecruiters, Recruitee
- **Official public job board APIs** — the same feeds companies use to power their own career pages. No login, no proxies, no blocking.
- **Unified fields**: title, department, team, location(s), country, remote / workplace type, employment type, posted date, job & apply URL, **salary when the company publishes it** (Ashby, Lever, Recruitee), plain-text description.
- **Filters**: title keywords, locations, remote only, posted within N days, max jobs per company.
- **$1.00 per 1,000 jobs.** You pay only for jobs returned.

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
  "salaryText": "$180K – $250K", "descriptionText": "…"
}
```
Normalized across all ATS: `countryCode` (ISO 3166-1 alpha-2), `employmentType` (`FULL_TIME`, `PART_TIME`, `CONTRACT`,
`TEMPORARY`, `INTERN`, `OTHER`), `workplaceType` (`remote`, `hybrid`, `onsite`), `salaryInterval` (`hour`, `day`, `week`,
`month`, `year`), numeric salaries, trimmed text. The original values are kept in `*Raw` fields and `country`.
When an ATS does not return a company name (Lever, Ashby), `company` is derived from the board name.
Fields an ATS does not provide are `null`. A per-company summary (found / not found, jobs on board, jobs returned)
is saved to the key-value store as `SUMMARY`.

## Pricing
Pay per event: **$0.001 per job returned** ($1.00 per 1,000). Filters are applied before charging.
Set a max charge per run in the run options to cap spend.

## Use cases
- Hiring-signal and sales-intelligence feeds (which companies are hiring for what)
- Job boards and niche job aggregators
- Labour-market and compensation research

## Notes
- Data comes only from public job board APIs that companies publish for their own career pages. No personal data is collected.
- SmartRecruiters does not include descriptions in its list API, so `descriptionText` is empty for SmartRecruiters jobs.
- Not affiliated with any ATS vendor.

## Related tools
- [job-postings-normalizer](https://apify.com/ucchiii/job-postings-normalizer) — Clean and dedupe job data from LinkedIn, Indeed and other scrapers
- [dataset-dedup-merge](https://apify.com/ucchiii/dataset-dedup-merge) — Merge several job runs and remove duplicates
- [rss-feed-reader](https://apify.com/ucchiii/rss-feed-reader) — Follow company blogs and news feeds next to hiring signals

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
