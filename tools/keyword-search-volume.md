# Keyword Research: Search Volume, CPC (DataForSEO key)

**Run it on Apify:** https://apify.com/ucchiii/keyword-search-volume · **Price:** $20.00 per 1,000 lookup batch (up to 1,000 keywords)s · free demo with empty input

**For SEO and PPC teams who have a keyword list and need numbers to prioritize it.** Get Google Ads monthly search
volume, CPC, competition, top-of-page bid range and 12-month trends for up to 10,000 keywords per run, in any country
and language. You use **your own [DataForSEO](https://dataforseo.com/) API login and password**.
**You need a DataForSEO account (API login and password).** DataForSEO bills your account for each lookup
(Google Ads search volume, roughly $0.05–$0.09 per request of up to 1,000 keywords — check
[DataForSEO pricing](https://dataforseo.com/pricing) for your rate). On top of that, this Actor charges
**$0.02 per batch of up to 1,000 keywords + $0.001 per keyword with data** (e.g. 10 keywords ≈ $0.03,
1,000 keywords ≈ $1.02). Keywords without data are not charged. Without your DataForSEO login the Actor stops with an
error and charges nothing. Leave the input empty for a free demo with sample data.

## Use cases
- **SEO content planning:** rank topic ideas by monthly search volume and see which ones are growing (`trend3mPct`, `trend12mPct`).
- **PPC budgeting:** estimate cost with `cpc`, `lowTopOfPageBid` and `highTopOfPageBid` before you launch a campaign.
- **Keyword list prioritization:** enrich a list from another tool or a spreadsheet and sort by volume, competition and trend.

## Bring your own DataForSEO key
This Actor sends your keywords to the DataForSEO Keywords Data API (Google Ads search volume) with **your** API login and
API password, so the data usage is billed by DataForSEO to your own account. You pay this Actor only the prices above.
Find the credentials in your DataForSEO dashboard under **API Access**.
- Your login and password are used only for this run. They are never stored by us, never written to the log, the
  dataset or the key-value store, and are masked in error messages. The input fields are marked secret.
- Wrong credentials fail the run with a clear message and nothing is charged.
- You use DataForSEO under **your own agreement with DataForSEO** (you accept their terms directly and are responsible for your account).
  DataForSEO is not available in some countries and regions (for example Russia, Belarus, Cuba, Iran, Syria, Myanmar, North Korea and occupied territories such as Crimea) — see the DataForSEO terms.
- Duplicate keywords in your input (ignoring case and extra spaces) are removed before sending, so you never pay DataForSEO twice for the same keyword in one run.

## Input
```json
{ "keywords": ["keyword research", "seo tools"], "location": "2840", "language": "en", "includeMonthly": true,
  "dataforseoLogin": "you@example.com", "dataforseoPassword": "your-api-password" }
```
- `keywords`: up to 10,000 per run (trimmed, de-duplicated ignoring case; optional `lowercaseKeywords`). Sent in tasks of up to 1,000.
- `location`: DataForSEO location code (2840 United States, 2826 United Kingdom, 2392 Japan) or a name such as "United States". Default 2840.
- `language`: language code, default `en`.
- `includeMonthly`: include the monthly series (default true).

## Output
One row per keyword (illustrative values, not real data):
```json
{ "keyword": "keyword research", "location": 2840, "language": "en", "searchVolume": 14800, "cpc": 5.21,
  "competition": "HIGH", "competitionIndex": 91, "lowTopOfPageBid": 2.1, "highTopOfPageBid": 9.8,
  "monthlySearches": [{"year": 2026, "month": 9, "searchVolume": 15400}, "..."],
  "trend3mPct": 4.2, "trend12mPct": 11.8, "dataAvailable": true, "error": null }
```
`trend3mPct` compares the average of the newest 3 months with the 3 months before; `trend12mPct` compares the newest 3
months with the oldest 3 of the newest 12 months. Both are `null` when there is not enough history.

## Cost record
Each run saves a `COST` record (number of DataForSEO tasks and the estimated DataForSEO cost) in its key-value store, so you can check what the run used on your DataForSEO account.

## Charging and limits
- `keyword-batch` ($0.02) is charged once per DataForSEO lookup task (each started chunk of up to 1,000 keywords), only after that task succeeded.
- `keyword` ($0.001) is charged only for rows where search volume data was returned. Rows without data (`dataAvailable: false`) are written but not charged.
- Your maximum charge per run is respected: no lookup is sent that the remaining budget cannot pay for.
- Free demo (no keywords and no credentials): a fixed sample table marked `_demo: true`, "sample data, not real volumes". DataForSEO is not called.
- Keywords given without credentials fail the run, no charge. Google Ads data has no values for some sensitive categories; such keywords come back without data.

<!-- more-tools:start -->
## More tools from the same developer

- [SERP Keyword Clustering](https://apify.com/ucchiii/serp-keyword-clustering) — group keywords by shared search results
- [SERP Analyzer](https://apify.com/ucchiii/serp-dataset-analyzer) — rankings, share of voice and People Also Ask from Google Search scraper datasets
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
