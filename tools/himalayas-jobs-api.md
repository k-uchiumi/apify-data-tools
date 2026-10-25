# Himalayas Remote Jobs API: Export Job Listings

**Run it on Apify:** https://apify.com/ucchiii/himalayas-jobs-api · **Price:** $1.00 per 1,000 jobs · free demo with empty input

**For recruiters, job-board builders, sales-intelligence teams and analysts who want a clean list of current remote jobs.**
Set a keyword, seniority, country or timezone and get **one flat row per job** from the public [Himalayas](https://himalayas.app) Jobs API. **$1.00 per 1,000 jobs**, you pay only for jobs returned.

**Try it free: leave input empty and press Start.** You get 3 sample jobs, not charged, marked `_demo: true`.

- **Official public API**: reads `himalayas.app/jobs/api` and `/jobs/api/search`. No login, no proxies, no HTML scraping.
- **Filters**: keyword (title, company, description), category, seniority, employment type, country, worldwide only, timezone, sort order.
- **Flat, normalized rows**: title, company, Himalayas job URL, location restrictions, timezones, seniority, employment type, salary min/max/currency/period when listed, posted and expiry dates, categories, plain-text description.
- **Contact details removed**: e-mail addresses and phone numbers in descriptions are replaced with `[removed]`.
- **Polite**: one request at a time, about 1 second apart, retries with backoff on 429 / 5xx.

## Sample output
| title | company | location | seniority | salary | postedAt | url |
|---|---|---|---|---|---|---|
| Senior Python Developer | Acme | Worldwide | Senior | 90,000-120,000 USD annual | 2026-10-20 | https://himalayas.app/companies/acme/jobs/senior-python-developer |
| Data Engineer | Globex | Germany, France | Mid-level | n/a | 2026-10-19 | https://himalayas.app/companies/globex/jobs/data-engineer |

## Input
```json
{
  "keywords": ["python"],
  "categories": ["Developer"],
  "seniority": ["Senior"],
  "employmentType": ["Full Time"],
  "countries": ["US", "Germany"],
  "worldwideOnly": false,
  "timezone": "UTC+1",
  "sort": "recent",
  "maxJobs": 20
}
```
- `keywords`: jobs matching any word in title, company or description. Each keyword is also sent to Himalayas as a search query (one search per keyword and country, at most 20 combinations).
- `categories`: matched against category and parent category (e.g. `Developer`, `Sales`) after fetching, so rare categories scan more pages.
- `seniority`: `Entry-level`, `Mid-level`, `Senior`, `Manager`, `Director`, `Executive`. `employmentType`: `Full Time`, `Part Time`, `Contractor`, `Temporary`, `Intern`, `Volunteer`, `Other`.
- `timezone`: UTC offset such as `-5`, `UTC+1`, `UTC+05:30`.
- `maxJobs`: 1 to 2,000 (prefill 20). A run scans at most 8,000 listings.

## Output (one item per job)
```json
{
  "source": "Himalayas",
  "title": "Senior Python Developer", "company": "Acme", "companySlug": "acme",
  "url": "https://himalayas.app/companies/acme/jobs/senior-python-developer",
  "applyUrl": "https://himalayas.app/companies/acme/jobs/senior-python-developer",
  "location": "Germany, France", "locationRestrictions": ["Germany", "France"], "worldwide": false,
  "timezones": ["UTC-1", "UTC+0", "UTC+1"],
  "seniority": "Senior", "employmentType": "Full Time",
  "salaryMin": 90000, "salaryMax": 120000, "salaryCurrency": "USD", "salaryPeriod": "annual",
  "postedAt": "2026-10-20T08:00:00Z", "expiresAt": "2026-11-20T08:00:00Z",
  "categories": ["Python"], "parentCategories": ["Developer"],
  "descriptionText": "We are looking for ...", "descriptionTruncated": false
}
```
- Salaries are as published by Himalayas and are **not** converted to annual: read `salaryPeriod` (`hourly`, `weekly`, `fortnightly`, `monthly`, `annual`).
- `location` lists the countries candidates must be in; "Worldwide" means no restriction.
- `descriptionText` is cut at 30,000 characters (`descriptionTruncated`).
- A run summary (jobs returned, listings scanned, requests, warnings) is saved to the key-value store as `SUMMARY`.

## Pricing
Pay per event: **$0.001 per job returned** (1,000 jobs cost $1.00). Filters are applied before charging. The empty-input demo and failed runs are free. Set a max charge per run in the run options to cap spend.

## Attribution and terms (Himalayas)
Job data comes from the Himalayas public API and is subject to its terms ([API docs](https://himalayas.app/docs/remote-jobs-api)). If you display or republish these listings you must:
- **credit Himalayas as the source** with a visible link, and link each job to its Himalayas page (the `url` field);
- **not resubmit** Himalayas listings to third-party job aggregators or job sites (the docs name Jooble, Neuvoo, Google Jobs and LinkedIn Jobs as examples).

Himalayas refreshes its data about every 24 hours, so running more than once a day adds nothing. You are responsible for following these terms when you use the data.

## Use cases
- Remote job alerts for a niche (for example senior Python, worldwide)
- Hiring-signal and market research by category, seniority and salary
- Content for your own remote-jobs site, with proper credit to Himalayas

## Notes
- No personal data is collected. E-mail addresses and phone numbers in descriptions are removed.
- Not affiliated with Himalayas.

<!-- more-tools:start -->
## More tools from the same developer

- [Greenhouse Jobs Scraper](https://apify.com/ucchiii/greenhouse-jobs) — open jobs from any Greenhouse job board via the public Job Board API
- [Lever Jobs Scraper](https://apify.com/ucchiii/lever-jobs) — open postings from any company on Lever via the public Postings API
- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
- [Job Market Report](https://apify.com/ucchiii/job-market-report) — salary percentiles, top skills, remote share and role/country segments from normalized job data
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
