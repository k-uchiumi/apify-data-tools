# SERP Keyword Clustering: group keywords by shared URLs

**Run it on Apify:** https://apify.com/ucchiii/serp-keyword-clustering · **Price:** $2.00 per 1,000 clustered keywords · free demo with empty input

SEO teams and content planners: turn a big keyword list into **topic groups** by checking which keywords Google answers
with the same URLs, so you write one page per group instead of one per keyword. It reads datasets you already have from
`apify/google-search-scraper`; no Google access, no proxies, no LLM.
**$2.00 per 1,000 keywords**. Reading datasets is free.

## Use cases
- Plan one page per topic instead of one page per keyword
- Find keyword cannibalization: keywords that share results but point to different pages
- Group a keyword research export into topics with an intent label (informational, commercial, navigational)

## How it works
1. Run **Google Search Scraper** (`apify/google-search-scraper`) on your keywords.
2. Run **this Actor** with that run's dataset ID.
3. Read one row per keyword with its cluster, parent keyword and estimated intent. A full cluster list is saved in the
   key-value store as `CLUSTERS`.

For each keyword the top N organic URLs (default 10) are compared. Two keywords with at least `minSharedUrls` (default 3)
URLs in common belong together. URLs are compared without `www`, query string, fragment or trailing slash.

- **strict** (default): the most connected keyword becomes the centre of a group, and only keywords that overlap with it
  join. Tight, focused groups.
- **loose**: connected components. Any chain of overlaps merges into one group, so groups are larger and can drift
  across topics.
- **parentKeyword**: the keyword in the group with the highest total overlap with the others.
- **Intent** (`informational`, `commercial`, `transactional`, `navigational`, `local`) is an **estimate from rules**: words
  in the keyword (buy, price, best, vs, how, near me, login, ...) plus URL patterns in the top results (/product, /shop,
  /blog, wikipedia, youtube, ...). `intentConfidence` is `low`, `medium` or `high`. Treat it as a hint, not a fact.
- Missing fields in the source data are tolerated. By default each country and device combination is clustered separately.

## Input
```json
{
  "datasetIds": ["<dataset ID of your Google Search scraper run>"],
  "topN": 10,
  "minSharedUrls": 3,
  "clusterMethod": "strict",
  "groupByCountry": true
}
```
Leave `datasetIds` empty to see a free demo on sample data (rows carry `_demo: true`).

## Output
One row per keyword:
```json
{"keyword": "buy running shoes", "clusterId": 2, "parentKeyword": "running shoes price", "clusterSize": 3,
 "sharedUrlsWithParent": 4, "intent": "transactional", "intentConfidence": "high", "countryCode": "US",
 "device": "DESKTOP", "topUrls": ["https://a.com/shop", "https://b.com/product/1", "https://c.com/store"]}
```
`sharedUrlsWithParent` is empty (null) for the parent itself. In `strict` mode a keyword is joined through the group's
centre, which can differ slightly from the parent, so its overlap with the parent may be below your threshold.

Key-value store `CLUSTERS`:
```json
[{"clusterId": 2, "parentKeyword": "running shoes price", "keywords": ["running shoes price", "buy running shoes"],
  "size": 2, "commonUrls": ["https://a.com/shop"], "countryCode": "US", "device": "DESKTOP"}]
```
`SUMMARY` holds counts (keywords, clusters, singletons) and the settings used.

## Pricing
$2.00 per 1,000 keywords written (event `keyword`, $0.002 each). The demo is free.

## Notes
This Actor only processes datasets you already own; it does not access Google. You are responsible for how the
underlying data was collected and used.

<!-- more-tools:start -->
## More tools from the same developer

- [SERP Analyzer](https://apify.com/ucchiii/serp-dataset-analyzer) — rankings, share of voice and People Also Ask from Google Search scraper datasets
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
