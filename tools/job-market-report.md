# Job Market Report: salary percentiles, skills, remote share

**Run it on Apify:** https://apify.com/ucchiii/job-market-report · **Price:** $100.00 per 1,000 reports · free demo with empty input

**For recruiters, HR and compensation analysts, career coaches, job-board and newsletter owners, and labour-market
researchers who already scrape jobs and now have to answer "what does this market actually pay, ask for and allow?"**
You have thousands of job postings in an Apify dataset but no time to turn them into numbers. This Actor reads the
**normalized** job dataset (the output of [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer))
and writes **one market report plus one row per role or country** — no scraping, no proxies, no spreadsheets.
**$0.10 per report + $5 per 1,000 segment rows.** Reading and duplicates are free.

## What you get
- **Salary percentiles p25 / p50 / p75 per currency** — annualized (hour × 2,080, day × 260, week × 52, month × 12),
  using the midpoint of the salary range. **Currencies are never mixed or converted.**
- **Most requested skills** — the share of jobs that mention each of about 100 common skills (Python, SQL, AWS, Kubernetes,
  Salesforce, Figma, Excel …) in the title or description. Add your own tools with `extraSkills`.
- **Remote share** — remote / hybrid / onsite counts and `remoteShare` (remote ÷ jobs with a known workplace type).
- **Segments** — one row per **role family** (derived from the job title: Data Analysis, DevOps & Cloud, Sales …), per
  **country**, or per **role × country**, each with its own salary percentiles, remote share, top skills and top hiring companies.
- **Report row** — jobs analyzed, salary coverage, top roles, top countries, top 10 hiring companies, employment types,
  posting date range. The same report is saved as one JSON document in the key-value store (`REPORT`).

## Use cases
- Benchmark pay for a role in a country before you write an offer or negotiate
- Show candidates or clients which skills a market asks for and how many roles are remote
- Fill a weekly "jobs market" newsletter or dashboard with fresh numbers from your own scraper runs
- Compare countries or role families in a labour-market study

## How to use
1. Run a job scraper (LinkedIn Jobs, Indeed, Glassdoor, career pages …) and note the dataset ID.
2. Run **Job Postings Normalizer** on it (several datasets can be merged there). It turns salaries and countries into
   the fields this Actor reads.
3. Run this Actor with the normalizer's dataset ID in `datasetIds`. Open the first dataset row (`type: "report"`) and the
   segment rows, or fetch the `REPORT` record.

Leave `datasetIds` empty to see a **free demo** on clearly fictional sample jobs (every row is marked `"_demo": true`).

## Input
```json
{
  "datasetIds": ["<dataset ID of a Job Postings Normalizer run>"],
  "segmentBy": "roleAndCountry",
  "maxSegments": 30,
  "minSalarySample": 3,
  "topSkills": 20,
  "extraSkills": ["dbt", "Terraform"],
  "dedup": true
}
```
| Field | Default | Meaning |
|---|---|---|
| `segmentBy` | `role` | `role`, `country` or `roleAndCountry` |
| `maxSegments` | 30 (0–200) | Largest segments first. Also caps segment charges. 0 = report row only |
| `minSalarySample` | 3 | Percentiles are `null` when fewer salaries of one currency are available |
| `topSkills` | 20 (1–50) | Skills listed in the report row |
| `extraSkills` | none | Extra tools to count (whole-word, case-insensitive) |
| `dedup` | true | Same URL, then same company + title + location counted once |

Fields read from each job: `title`, `company`, `countryCode`, `workplaceType` (or `remote`), `salaryMin`, `salaryMax`,
`salaryCurrency`, `salaryInterval`, `postedAt`, `employmentType`, `description` (only to detect skills; it is not output).
A job counts for salary only when it has a **currency, an interval and an amount**. Datasets that were not normalized
still work but give sparse salary and country numbers.

## Output
First row: the report.
```json
{
  "type": "report", "jobsAnalyzed": 24, "companies": 7, "countries": 5, "salaryCoverage": 0.833, "salaryUnusable": 1,
  "salaryByCurrency": {
    "USD": {"sample": 9, "p25": 125000, "p50": 147500, "p75": 170000, "interval": "year"},
    "EUR": {"sample": 5, "p25": 75000, "p50": 79000, "p75": 80000, "interval": "year"}
  },
  "workplace": {"remote": 8, "hybrid": 10, "onsite": 6, "unknown": 0, "remoteShare": 0.333, "remoteOrHybridShare": 0.75},
  "topSkills": [{"skill": "SQL", "jobs": 10, "share": 0.417}, {"skill": "Python", "jobs": 7, "share": 0.292}],
  "topRoles": [{"role": "Software Engineering", "jobs": 5}], "topCountries": [{"countryCode": "US", "jobs": 10}],
  "topCompanies": [{"company": "Demo Systems (sample)", "jobs": 5}], "postedFrom": "2026-10-06", "postedTo": "2026-10-14"
}
```
Then one row per segment (`type: "segment"`):
```json
{
  "type": "segment", "segmentType": "country", "role": null, "countryCode": "US", "jobs": 10, "jobsWithSalary": 9,
  "salaryCurrency": "USD", "salarySample": 9, "salaryP25": 125000, "salaryP50": 147500, "salaryP75": 170000,
  "salaryInterval": "year", "remoteShare": 0.4, "remote": 4, "hybrid": 4, "onsite": 2,
  "salaryByCurrency": {"USD": {"sample": 9, "p25": 125000, "p50": 147500, "p75": 170000, "interval": "year"}},
  "topSkills": [{"skill": "SQL", "jobs": 4}], "topCompanies": [{"company": "Demo Systems (sample)", "jobs": 2}]
}
```
In a segment row `salaryCurrency` and the `salaryP*` columns show the currency with the most salaries in that segment;
all currencies are in `salaryByCurrency`. Run statistics are in the key-value store as `STATS`.

## Pricing
Pay per event: **$0.10 per report** (one per run; not charged when no jobs could be analyzed) and **$5 per 1,000
segments** ($0.005 per segment row). A typical run — one report and 30 segments — costs about **$0.25**, whatever the
number of jobs analyzed. `maxSegments` and the maximum cost per run in Apify cap the spend; the Actor stops cleanly when
the limit is reached. The demo is free.

## Notes and limits
- This Actor only reads datasets you already own; it does not access any job site. You are responsible for how the
  underlying data was collected and used.
- **The numbers describe your dataset, not the whole market.** A scrape of one job board, one search term or one week is a
  sample; small segments are noisy, so percentiles need `minSalarySample` salaries.
- Role families come from keyword rules on the job title (about 25 groups); unusual titles fall into "Other".
  Skills are keyword matches, so "Go" is counted only as "Golang" or "Go developer/programming" and a skill mentioned in a
  "nice to have" line counts too.
- Salaries without a currency or interval are left out of percentiles (and counted in `salaryUnusable`). Annualization uses
  fixed factors; no currency conversion and no cost-of-living adjustment.
- No personal data is output: no job texts, URLs, applicant or recruiter names. Company names appear only as hiring counts.

<!-- more-tools:start -->
## More tools from the same developer

- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
- [Job Listings API](https://apify.com/ucchiii/career-site-jobs) — every open job from Greenhouse, Lever, Ashby, Workable, SmartRecruiters and Recruitee career sites
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
