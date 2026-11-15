# Workable Jobs Scraper: All Open Jobs per Company

**Run it on Apify:** https://apify.com/ucchiii/workable-jobs · **Price:** $0.70 per 1,000 jobs · free demo with empty input

**Workable jobs scraper: every open job from the public Workable job boards you list, one clean row per job.**

Give it company names or `apply.workable.com` links and get each company's open jobs with title, location, remote flag, department, employment type, posted date, description and apply link, ready to download as CSV, Excel or JSON. It reads the same public job-board data that a company's Workable careers page shows, so there is no login, no browser and no proxy.

**Try it free:** clear the company list and press **Start**: you get 3 jobs from one public Workable board, not charged (rows carry `_demo: true`).

## What you get

| company | title | location | remote | department | employmentType | postedAt | applyUrl |
|---|---|---|---|---|---|---|---|
| Acme Robotics | Senior Backend Engineer | Paris, Île-de-France, France | false | Engineering | FULL_TIME | 2026-10-28T00:00:00Z | https://apply.workable.com/j/AB12CD34/apply |
| Acme Robotics | Account Executive | Berlin, Germany | true | Sales | FULL_TIME | 2026-10-21T00:00:00Z | https://apply.workable.com/j/EF56GH78/apply |

- **Input as you have it:** `huggingface`, `https://apply.workable.com/huggingface/`, a job link on that board, or a company's own Workable subdomain (`acme.workable.com`).
- **Filters:** title contains, location contains (city, country, country code or "remote"), department contains, remote only, posted within N days, max jobs per company.
- **Same fields as Career Site Jobs** (Greenhouse, Lever, Ashby and more, from the same developer), plus Workable extras (`function`, `industry`, `experience`, `education`, `createdAt`), so datasets can be combined.
- **Polite:** one company at a time with a pause between requests, and retries with backoff when Workable asks to slow down.

## How to use

1. **Companies**: one per line (up to 200).
2. Optional filters; leave them empty for all open jobs.
3. Press **Start** and download the dataset.

```json
{
  "companies": ["https://apply.workable.com/huggingface/", "acme"],
  "titleKeywords": ["engineer", "scientist"],
  "locations": ["Paris", "Remote"],
  "postedWithinDays": 30,
  "maxJobsPerCompany": 100
}
```

## Output fields

`company`, `boardSlug`, `jobId` (Workable shortcode), `title`, `department`, `location`, `locations`, `city`, `state`, `country`, `countryCode`, `remote`, `workplaceType`, `employmentType` (FULL_TIME, PART_TIME, CONTRACT, TEMPORARY, INTERN or OTHER) and `employmentTypeRaw`, `postedAt`, `createdAt`, `url`, `applyUrl`, `function`, `industry`, `experience`, `education`, `descriptionText` (plain text), `descriptionHtml` (optional), `descriptionTruncated`.

A **STATS** record lists, per company, whether its board was found, how many open jobs it has and how many were returned.

## Limits (cannot be raised by input)

- 200 companies per run. Descriptions are cut at 30,000 characters.
- Only public Workable job boards. Companies whose board is private or that use another job system are reported as `not_found` and are free.
- Set a maximum charge per run to cap spend: the Actor stops cleanly at that number of jobs.

## Pricing

Pay per event: **$0.0007 per job** ($0.70 per 1,000 jobs), only for jobs returned after your filters. Companies that are not found, failed requests and the demo are free.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 7,100 jobs a month.

## FAQ

**Where do I find a company's Workable account name?** Open its careers page; if the job links go to `apply.workable.com/<name>/`, that `<name>` is it. Pasting the whole link works too.

**Does it include closed jobs?** No. The public board shows only open jobs, so each run is a snapshot of what is open now. Schedule the Actor to track new jobs over time.

**Is it affiliated with Workable?** No. It reads public job-board data the companies publish themselves.

**Does it collect personal data?** No. It returns job postings only, not candidate or recruiter profiles.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Greenhouse Jobs Scraper](https://apify.com/ucchiii/greenhouse-jobs) — open jobs from any Greenhouse job board via the public Job Board API
- [Lever Jobs Scraper](https://apify.com/ucchiii/lever-jobs) — open postings from any company on Lever via the public Postings API
- [Ashby Jobs Scraper](https://apify.com/ucchiii/ashby-jobs) — open jobs from any company on Ashby via the public Posting API, with published pay ranges
- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
