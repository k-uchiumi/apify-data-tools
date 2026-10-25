# Job Postings Normalizer: Clean LinkedIn & Indeed Job Data

**Run it on Apify:** https://apify.com/ucchiii/job-postings-normalizer · **Price:** $0.50 per 1,000 jobs · free demo with empty input

**For job-board builders, recruiters and labour-market analysts who collect jobs from several scrapers.** You scraped
LinkedIn Jobs, Indeed, Glassdoor or company career pages and now have **five different field names for the same thing,
salaries as free text, duplicates across runs and dates like "3 days ago"**. This Actor turns the output of *any* job
scraper into one **flat, consistent job list** in a single run — no scraping, no proxies. **$0.50 per 1,000 jobs written.**

- **Unified fields**: `title` / `jobTitle` / `positionName` → `title`; `company` / `companyName` → `company`;
  `location` / `jobLocation`; `url` / `jobUrl` / `link`; `salary` / `salaryText` / `salaryRange`;
  `postedAt` / `datePosted` / `publishedAt` / `postedTime` and more
- **Salary parsing**: `"$120,000 - $150,000 a year"`, `"€50k–60k"`, `"£25 per hour"`, `"₹12 LPA"` become
  `salaryMin`, `salaryMax`, `salaryCurrency` (ISO 4217) and `salaryInterval` (`hour`, `day`, `week`, `month`, `year`)
- **Employment type**: `FULL_TIME`, `PART_TIME`, `CONTRACT`, `TEMPORARY`, `INTERN`, `OTHER`
- **Workplace type**: `remote`, `hybrid`, `onsite`
- **Country code** (ISO 3166-1 alpha-2) from the country field or the end of the location text
- **Dates** as UTC ISO 8601 — also from "3 days ago", "Just posted", epoch timestamps
- **Deduplication** across several runs: same URL first, then same company + title + location
- **Filters**: title keywords, countries, remote only, minimum yearly salary, posted within N days
- Original values are kept in `*Raw` fields (`salaryRaw`, `employmentTypeRaw`, `postedAtRaw`, …); surrounding whitespace is trimmed
- **No contact data**: e-mail addresses and phone numbers in descriptions are replaced with `[email removed]` / `[phone removed]`, and `mailto:` / `tel:` links are dropped
- **$0.50 per 1,000 jobs written.** Reading, duplicates and filtered-out rows are free.

Works with the datasets of popular job scrapers and anything that uses similar field names.

## Use cases
- Merge LinkedIn, Indeed and Glassdoor scraper runs into one deduplicated job feed
- Load jobs with numeric salaries and country codes into a database or BI tool
- Filter to remote, well-paid or recently posted roles for a job alert or newsletter

## Input
```json
{
  "datasetIds": ["<dataset ID of your job scraper run>"],
  "dedup": true,
  "titleKeywords": ["engineer"],
  "countries": ["US", "Germany"],
  "remoteOnly": false,
  "minSalary": 80000,
  "postedWithinDays": 14
}
```
Leave `datasetIds` empty to see a free demo on clearly fictional sample jobs (marked `"_demo": true`).

Notes on filters: `minSalary` compares the top salary converted to a yearly amount (hourly × 2,080, daily × 260,
weekly × 52, monthly × 12); currencies are **not** converted. Jobs with no salary, no readable date or no known
country are removed when the matching filter is set.

## Output (one row per job)
```json
{
  "title": "Backend Engineer", "company": "Acme", "location": "Berlin, Germany", "countryCode": "DE",
  "remote": false, "workplaceType": "hybrid", "employmentType": "FULL_TIME",
  "salaryMin": 60000, "salaryMax": 80000, "salaryCurrency": "EUR", "salaryInterval": "year",
  "postedAt": "2026-10-16T12:00:00Z", "url": "https://www.linkedin.com/jobs/view/123", "applyUrl": null,
  "jobId": "123", "description": "…",
  "countryRaw": null, "employmentTypeRaw": "Full-time", "workplaceTypeRaw": "Hybrid",
  "salaryRaw": "€60k–80k", "postedAtRaw": "2 days ago", "sourceDatasetId": "…"
}
```
Run statistics (`itemsRead`, `duplicates`, `filteredOut`, `written`, `stoppedAtChargeLimit`) are saved in the
key-value store as `STATS`.

## Pricing
Pay per event: **$0.50 per 1,000 jobs** written to the output (`job` event). Set a maximum cost per run and the Actor
stops cleanly when it is reached.

## Notes
This Actor only processes datasets you already own; it does not access any job site. Salary and country detection are
best-effort: ambiguous values are left empty rather than guessed (a number without a currency sign has no currency). You
are responsible for how the underlying data was collected and used.

<!-- more-tools:start -->
## More tools from the same developer

- [Job Listings API](https://apify.com/ucchiii/career-site-jobs) — every open job from Greenhouse, Lever, Ashby, Workable, SmartRecruiters and Recruitee career sites
- [Job Market Report](https://apify.com/ucchiii/job-market-report) — salary percentiles, top skills, remote share and role/country segments from normalized job data
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Japanese Text Toolkit](https://apify.com/ucchiii/japanese-text-toolkit) — furigana, romaji, wareki dates and normalization for Japanese text
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
