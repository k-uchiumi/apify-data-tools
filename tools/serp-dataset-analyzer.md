# SERP Analyzer: Rankings, Share of Voice, People Also Ask

**Run it on Apify:** https://apify.com/ucchiii/serp-dataset-analyzer · **Price:** $1.00 per 1,000 rows · free demo with empty input

**For SEO teams and agencies that already scrape Google results** with `apify/google-search-scraper`. This Actor turns
that raw dataset into **the tables an SEO team actually reads** — rank tracking, share of voice, People Also Ask —
with no Google access, no proxies, no scraping. **$1.00 per 1,000 rows written.**

- **Your best position per keyword** (and the ranking URL) for one or more of your domains
- **Top 3 domains** per keyword, organic and paid result counts, whether People Also Ask appears
- **Domain table**: appearances, number of keywords, average and best position, top-3 count, **share of voice**
  (share of all top-10 organic results)
- **Related queries** and **People Also Ask questions** as flat rows, tagged with the keyword they came from
- Tolerates missing fields; country and device variants of a keyword are kept separate
- Reading datasets is free.

## Use cases
- Rank tracking: your best position and ranking URL per keyword
- Competitor share of voice across a keyword list
- Content ideas from People Also Ask and related queries

## Input
```json
{
  "datasetIds": ["<dataset ID of your Google Search scraper run>"],
  "yourDomains": ["example.com"],
  "topN": 10
}
```
Leave `datasetIds` empty to see a free demo on sample data (rows carry `_demo: true`).

## Output
Every row has a `type`:
```json
{"type": "keyword", "term": "running shoes", "countryCode": "US", "device": "DESKTOP", "yourBestPosition": 4,
 "yourUrl": "https://www.example.com/shoes", "top3Domains": ["a.com", "b.com", "c.org"], "organicCount": 10,
 "paidCount": 1, "hasPeopleAlsoAsk": true}
{"type": "domain", "domain": "a.com", "appearances": 12, "keywordsCount": 9, "avgPosition": 2.4, "bestPosition": 1,
 "top3Count": 8, "shareOfVoice": 0.1143}
{"type": "relatedQuery", "text": "running shoes for flat feet", "fromKeyword": "running shoes"}
{"type": "question", "text": "How often should I replace running shoes?", "fromKeyword": "running shoes"}
```
A run summary (pages read, keywords, domains, your coverage and average best position, keywords with ads / PAA) is
saved in the key-value store as `SUMMARY`.

## Pricing
$1.00 per 1,000 rows written (event `row`). The demo is free.

## Notes
This Actor only processes datasets you already own; it does not access Google. You are responsible for how the
underlying data was collected and used.

<!-- more-tools:start -->
## More tools from the same developer

- [SERP Keyword Clustering](https://apify.com/ucchiii/serp-keyword-clustering) — group keywords by shared search results
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
