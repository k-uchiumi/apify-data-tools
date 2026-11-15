# Workday Jobs Scraper: Jobs from Any Workday Career Site

**Run it on Apify:** https://apify.com/ucchiii/workday-jobs · **Price:** $1.30 per 1,000 jobs · free demo with empty input

**Open jobs from any Workday career site**

**All public jobs from any Workday career site, as JSON (or CSV/Excel)** — up to about 2,000 per search, Workday's own limit (see Notes).
**Input: just paste career-site URLs** such as `https://nvidia.wd5.myworkdayjobs.com/NVIDIAExternalCareerSite` — no login, no browser.
**Empty input = free demo. After that $1.30 per 1,000 jobs**, and you pay only for jobs returned.

For recruiters, sales-intelligence teams, labour-market analysts and job-board builders who need the open jobs of
companies that hire through Workday (large enterprises, universities, hospitals, banks). One clean row per job.

- **How it differs from [Career Site Jobs](https://apify.com/ucchiii/career-site-jobs):** that Actor covers Greenhouse, Lever, Ashby, Workable, SmartRecruiters and Recruitee; this one covers only **Workday** (`*.myworkdayjobs.com`), which those do not. Output fields are identical, so the datasets combine.
- **The public endpoints the career sites use themselves.** Workday career pages load their jobs from a JSON list and a JSON detail
  endpoint. The Actor calls the same endpoints: no login, no browser, no proxies.
- **Same fields as [Career Site Jobs](https://apify.com/ucchiii/career-site-jobs).** Datasets from both Actors can be merged as they are.
  Workday-specific extras: `jobReqId`, `timeType`, `locationsText`, `tenant`, `site`.
- **Filters**: keywords (the career site's own search), location contains, posted within N days, max jobs per site.
- **Polite by design**: one request at a time per host, at least 0.5 s between requests, timeouts, retries with backoff on 429/5xx.
  Sites that ask for a login are skipped (reported in the summary).
- **Free demo**: leave the input empty and the Actor returns a few jobs from two well-known Workday career sites, not charged.

## Input
```json
{
  "careerSiteUrls": [
    "https://nvidia.wd5.myworkdayjobs.com/NVIDIAExternalCareerSite",
    "https://salesforce.wd12.myworkdayjobs.com/en-US/External_Career_Site"
  ],
  "keywords": "data engineer",
  "locations": ["London", "Remote"],
  "postedWithinDays": 30,
  "maxJobsPerSite": 100
}
```
`careerSiteUrls` accepts `https://<tenant>.wdN.myworkdayjobs.com/<site>`, the same with a language segment
(`/en-US/<site>`, `/de-DE/<site>`), a link to a single job on that site, and `https://wdN.myworkdaysite.com/recruiting/<tenant>/<site>`.

| Field | Meaning |
|---|---|
| `keywords` | Search text passed to the career site's search. Empty = all jobs. |
| `locations` | Keep jobs whose location text contains any of these (case-insensitive). |
| `postedWithinDays` | Keep jobs posted in the last N days. |
| `maxJobsPerSite` | Stop after N matching jobs per site. |
| `includeDescription` / `includeDescriptionHtml` | Plain-text and/or HTML description. |
| `fetchDetails` | Default on. Off = list only: much faster, but the posted date is estimated from "Posted N days ago" and there is no description, country or time type. |

## Output (one item per job)
```json
{
  "company": "NVIDIA", "ats": "workday", "boardSlug": "nvidia/NVIDIAExternalCareerSite", "jobId": "JR1999999",
  "title": "Senior Software Engineer", "department": null, "team": null,
  "location": "US, CA, Santa Clara", "locations": ["US, CA, Santa Clara"], "country": "United States of America", "countryCode": "US",
  "remote": null, "workplaceType": null, "employmentType": "FULL_TIME", "employmentTypeRaw": "Full time",
  "postedAt": "2026-10-20T00:00:00Z", "updatedAt": null,
  "url": "https://nvidia.wd5.myworkdayjobs.com/en-US/NVIDIAExternalCareerSite/job/US-CA-Santa-Clara/Senior-Software-Engineer_JR1999999",
  "applyUrl": "https://nvidia.wd5.myworkdayjobs.com/en-US/NVIDIAExternalCareerSite/job/US-CA-Santa-Clara/Senior-Software-Engineer_JR1999999/apply",
  "salaryMin": null, "salaryMax": null, "salaryCurrency": null, "salaryInterval": null, "salaryText": null,
  "descriptionText": "…", "descriptionTruncated": false,
  "jobReqId": "JR1999999", "timeType": "Full time", "locationsText": "US, CA, Santa Clara", "tenant": "nvidia", "site": "NVIDIAExternalCareerSite"
}
```
Field names and normalized values (`countryCode`, `employmentType`, `workplaceType`) match Career Site Jobs. Workday does not publish
department or salary in these endpoints, so those fields are `null`. A per-site summary (ok, auth_required, not_found, error, jobs
on site, jobs returned) is saved to the key-value store as `SUMMARY`.

## Pricing
Pay per event: **$0.0013 per job returned** ($1.30 per 1,000). Filters are applied before charging; empty input (demo) is free.
On the Apify Free plan, the $5 of monthly platform credit covers about **3,800 jobs** a month — enough to try it on real data.
Set a max charge per run in the run options to cap spend.

## Use cases
- Hiring-signal feeds: which large employers hire for which roles, where
- Labour-market research on enterprise, public-sector and healthcare hiring
- Job boards and aggregators: combine with Greenhouse, Lever and Ashby jobs
- Weekly competitor hiring tracker with `keywords` and `postedWithinDays`

## Need Greenhouse, Lever, Ashby, Workable, SmartRecruiters or Recruitee?
Use [Career Site Jobs](https://apify.com/ucchiii/career-site-jobs): same output fields, same price.

## Notes
- Workday's job list returns at most about 2,000 jobs per search. For bigger career sites, run several searches with different keywords (keywords narrow the list on Workday's side; the location filter is applied after listing and does not). The run summary shows `listCapReached: true` when a site hit this limit, and a posting is never returned (or charged) twice.
- Data comes only from the public endpoints the career sites expose to every visitor. No personal data is collected.
- `postedWithinDays` and `locations` also skip detail requests for jobs that cannot match, which keeps runs short.
- Large sites take time because of the polite request gap: with details on, expect about 1 job per second.
- `descriptionText` and `descriptionHtml` are each cut at 30,000 characters; the text then ends with `…` and `descriptionTruncated` is `true`.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Job Listings API](https://apify.com/ucchiii/career-site-jobs) — every open job from Greenhouse, Lever, Ashby, Workable, SmartRecruiters and Recruitee career sites
- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Job Market Report](https://apify.com/ucchiii/job-market-report) — salary percentiles, top skills, remote share and role/country segments from normalized job data
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
