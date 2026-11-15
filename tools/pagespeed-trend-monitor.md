# PageSpeed Trend Monitor: Core Web Vitals Changes

**Run it on Apify:** https://apify.com/ucchiii/pagespeed-trend-monitor · **Price:** $3.00 per 1,000 page measurements · free demo with empty input

**For site owners, SEO teams and developers who check PageSpeed Insights every day and want to know what changed.**
Give a list of URLs and your own Google API key, schedule the Actor once a day, and every run tells you, per URL and device
(mobile and desktop):

- **Performance, FCP, TBT, Speed Index, LCP and CLS** today, the value from the **previous run**, the **change** and the **change in %**
- **Good / Needs improvement / Poor** before and after, with a flag when a metric **crosses a threshold** (for example LCP good -> needs improvement)
- a **verdict** per row: `worse`, `better`, `mixed`, `stable` or `first-run`, plus readable `alerts`
- **trend rows by date** (one row per URL, device and day) and a by-date table `TREND.csv` you can open in Excel or Google Sheets

The previous results are kept in a **named key-value store in your own Apify account**, so the comparison works across
scheduled runs. **$0.003 per page measurement** (one URL on one device). Failed measurements, a rejected key, trend rows
and the sample rows are free.

**Try it free: leave the input empty (or leave the API key empty) and press Start.** You get sample rows with fixed example
numbers (`_demo: true`); no API call is made and nothing is charged.

## Sample output

Measurement rows (the "Change since last run" view):

| url | strategy | verdict | performanceScore | performanceScoreChangePct | lcpMs | lcpMsPrev | lcpMsChangePct | lcpMsRatingPrev | lcpMsRating | cls | clsChangePct |
|---|---|---|---|---|---|---|---|---|---|---|---|
| https://www.python.org | mobile | mixed | 92 | 5.7 | 3151 | 3789 | -16.8 | needs-improvement | needs-improvement | 0.0359 | 1280.8 |

*(real row from two runs on Apify on 2026-11-13, a few minutes apart; percentages on tiny values such as CLS can look large)*

`alerts` for this row:
```json
["Performance better: needs-improvement -> good (87 -> 92, +5.7%)", "LCP better: 3789 ms -> 3151 ms, -16.8%", "CLS worse: 0.0026 -> 0.0359, +1280.8%"]
```

Trend rows (the "Trend by date" view; fixed example numbers, the same as the free sample rows):

| date | url | strategy | performanceScore | performanceScoreChangePct | lcpMs | lcpMsChangePct | cls |
|---|---|---|---|---|---|---|---|
| 2026-11-11 | https://www.example.com/ | mobile | 78 | | 2380 | | 0.04 |
| 2026-11-12 | https://www.example.com/ | mobile | 76 | -2.6 | 2450 | 2.9 | 0.04 |
| 2026-11-13 | https://www.example.com/ | mobile | 64 | -15.8 | 2910 | 18.8 | 0.04 |

## Set it up to run every day (Apify Schedules)

1. **Get a Google API key.** In the Google Cloud console, open a project, go to *APIs & Services > Library*, enable
   **PageSpeed Insights API**, then *APIs & Services > Credentials > Create credentials > API key*. Restricting the key
   to the PageSpeed Insights API is a good idea. The key belongs to you; its quota and any costs are on your Google project.
2. **Create a task.** Open this Actor in Apify Console, fill in `urls` and `apiKey` (stored encrypted as a secret input),
   choose the device, and set a `historyStoreName` such as `my-site-pagespeed`. Click *Save as a new task*.
3. **Run the task once** to create the history (all rows say `first-run`).
4. **Schedule it.** In Console go to *Schedules > Create new*, choose a daily time (for example `0 6 * * *` = every day
   06:00 UTC), and add your task. From the next run on, each row compares with the previous run.
5. **Get told about changes (optional).** On the task, add an integration or webhook (for example email or Slack on
   *Run succeeded*), and look at the `verdict`, `alerts` and `worseMetrics` fields, or at `SUMMARY` (counts of worse,
   better, stable rows).

Keep the same `historyStoreName` in every run that should be compared. Use different names for different sites or clients.

## Input

```json
{
  "urls": ["https://www.example.com/", "https://www.example.com/pricing"],
  "apiKey": "<your Google API key>",
  "strategy": "both",
  "historyStoreName": "my-site-pagespeed",
  "historyDays": 90,
  "trendDays": 7,
  "minChangePct": 10,
  "concurrency": 3
}
```

| Field | What it does |
|---|---|
| `urls` | Up to 200 URLs per run, `http` or `https`. Only measure pages you are allowed to test. |
| `apiKey` | **Required to measure.** Your own key with the PageSpeed Insights API enabled. Without it you only get the free sample rows. |
| `strategy` | `both` (default), `mobile` or `desktop`. `both` is two measurements per URL. |
| `historyStoreName` | Key-value store in your account that keeps the previous results (default `pagespeed-trend-history`). |
| `historyDays` | Days of history kept per URL and device, 1 to 400 (default 90). One entry per day; a second run on the same day replaces it. |
| `trendDays` | Trend rows per URL and device in the dataset, 0 to 90 (default 7). `TREND.csv` always has the whole kept history. |
| `minChangePct` | A change of at least this % (default 10) is flagged, as is any threshold crossing. |
| `concurrency` | Parallel measurements, 1 to 5 (default 3). |

## Output fields

Measurement rows (`rowType: "measurement"`), for each of `performanceScore`, `fcpMs`, `tbtMs`, `speedIndexMs`, `lcpMs`,
`cls` and the real-user (CrUX) `fieldLcpMs`, `fieldInpMs`, `fieldCls` when Google has them:

- `<metric>` (today), `<metric>Prev`, `<metric>Change`, `<metric>ChangePct`, `<metric>Rating`, `<metric>RatingPrev`
- `verdict`, `worseMetrics`, `betterMetrics`, `alerts`, `firstRun`, `previousCheckedAt`
- `url`, `strategy`, `date` (UTC), `checkedAt`, `finalUrl`, `error`

Trend rows (`rowType: "trend"`): `date`, `url`, `strategy`, the metrics, and the % change of the lab metrics against the
previous date.

Key-value store of the run: `TREND.csv` (one line per URL, device and metric, one column per date) and `SUMMARY`.

### How the ratings are decided
- FCP: good up to 1,800 ms, needs improvement up to 3,000 ms, poor above. LCP: 2,500 / 4,000 ms. CLS: 0.1 / 0.25.
  INP (field): 200 / 500 ms. These are the thresholds published in the PageSpeed Insights documentation.
- Performance score: 90+ good, 50–89 needs improvement, below 50 poor.
- TBT and Speed Index: from the Lighthouse metric score in the same response (0.9+ good, 0.5+ needs improvement), which is
  how the PageSpeed Insights report colours them.
- A change is flagged when it is at least `minChangePct` **and** larger than a small absolute amount (3 score points,
  100 ms, 50 ms for TBT, 0.02 for CLS), so that CLS 0.001 -> 0.002 (+100%) is not reported as a regression.

## Pricing
- **$0.003 per page measurement** (one URL on one device, measured successfully). 1 URL on mobile and desktop every day
  for 30 days = 60 measurements = **$0.18**. 20 URLs on both devices daily = about **$3.60 a month**.
- Free: sample rows, runs without an API key, rejected keys, quota errors, failed measurements, invalid URLs, trend rows,
  `TREND.csv`.
- Google does not charge this Actor for anything: the calls are made with your key against your Google project quota.

## FAQ

**Why do I need my own API key?** The Actor never uses a shared or built-in key. Your key gives you your own quota
(Google shows it in the Cloud console under *Quotas*) and keeps the use under your Google account, as Google's API terms expect.

**Is my key safe?** It is a secret input (encrypted by Apify). The Actor sends it only to Google's PageSpeed Insights
endpoint and never writes it to the log, the dataset, `SUMMARY`, `TREND.csv` or the history store. Errors are reported as
short codes only.

**What happens on the first run?** There is nothing to compare with yet, so every row is `first-run` with the current
values and ratings. The next run compares with it.

**I ran it twice today.** The second run compares with the first one, and replaces today's entry in the history (one
entry per day is kept).

**Scores jump around without any change on my site.** Lighthouse lab scores vary from run to run. Raise `minChangePct`
(for example 15 or 20) and look at `worseMetrics` over several days in the trend rows before acting.

**What errors can I see?** `api_key_invalid` (Google rejected the key), `api_key_forbidden_or_api_disabled` (the
PageSpeed Insights API is not enabled for the key's project, or the key is restricted), `daily_quota_exceeded`,
`rate_limited`, `timeout`, `page_unreachable_or_invalid`, `invalid_url`, `blocked_private_address`. After a key or daily
quota error the rest of the run is skipped. All of these are free.

## Notes and limits
- Data source: the Google PageSpeed Insights API v5 (`https://www.googleapis.com/pagespeedonline/v5/runPagespeed`), called
  with your key and `category=performance`. Google loads and measures the page; this Actor does not fetch it.
- This Actor is not affiliated with, sponsored or endorsed by Google. Your use of the API is under your Google account
  and the Google APIs Terms of Service.
- Results stay in your Apify account: the run's dataset and key-value store, and the named history store. The history
  keeps numbers only (scores, metrics and ratings), not the Lighthouse report. Delete the history store in Console
  (*Storage > Key-value stores*) whenever you want to start over.
- The history store must be created by this Actor (use a new name). A store created by another Actor may not be readable.
- Up to 200 URLs per run; each call has a 90 second timeout; 429 and 5xx responses are retried with backoff.
- Lab results come from a single Lighthouse run per URL and device; field data is the 28-day CrUX p75 when available.
- When testing this Actor without a key (for example Apify's automatic checks), it returns the free sample rows.

<!-- more-tools:start -->
## More tools from the same developer

- [SEO Audit Tool](https://apify.com/ucchiii/seo-audit-tool) — on-page SEO audit for URL lists and sitemaps: title, meta description, h1, canonical, noindex, robots.txt, issues and a 0-100 score
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [Redirect Checker](https://apify.com/ucchiii/redirect-checker) — full redirect chain for each URL, hop by hop, with loop detection
- [Website Screenshot](https://apify.com/ucchiii/website-screenshot) — screenshots and PDFs of web pages: full page, mobile, element, no cookie banners
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
