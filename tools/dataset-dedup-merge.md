# Dataset Merge & Dedup: Combine Datasets, Remove Duplicates

**Run it on Apify:** https://apify.com/ucchiii/dataset-dedup-merge · **Price:** $0.50 per 1,000 items · free demo with empty input

**For anyone running the same scraper more than once:** you end up with several Apify datasets full of overlapping items. This Actor merges them into one dataset and removes duplicates by the fields you choose (url, placeId, email, nested fields). **$0.50 per 1,000 output items; reading and comparing is free.**

Ran the same scraper several times, or several scrapers on overlapping inputs? This Actor **merges any number of
Apify datasets into one clean dataset and removes duplicates** by the fields you choose.

- **Dedup by any fields**: `url`, `placeId`, `title + company`, nested fields like `address.city`, or whole items
- **Smart matching**: ignore letter case, extra whitespace, full-width characters; optional URL normalization (http/https, www, trailing slash)
- **Keep first or last** (e.g. "newest run wins")
- **Output unique items or only the duplicates** (to audit what was removed)
- **Pick / omit fields**, optionally tag each item with its source dataset
- Streams items, so large datasets are fine
- **$0.50 per 1,000 output items.** Reading and comparing is free; you pay only for what is written.

## Input example
```json
{
  "datasetIds": ["aBcDeF123", "gHiJkL456"],
  "dedupFields": ["placeId"],
  "keep": "last",
  "normalizeUrls": true
}
```

## Output
The merged items in the default dataset. Run statistics are saved in the key-value store as `STATS`:
```json
{ "datasets": 2, "itemsRead": 15234, "duplicatesFound": 4120, "itemsWritten": 11114, "stoppedAtChargeLimit": false }
```

## Typical uses
- Combine Google Maps / jobs / e-commerce scraper runs and remove repeated places, jobs or products
- Merge daily runs into one master list keeping the latest version of each record
- Find which records appear in more than one dataset (`output: duplicatesOnly`)

## Pricing
Pay per event: **$0.0005 per output item** ($0.50 per 1,000). Set a max charge per run to cap spend.

## Related tools
- [maps-leads-cleaner](https://apify.com/ucchiii/maps-leads-cleaner) — Clean Google Maps scraper output into a CRM-ready lead list
- [job-postings-normalizer](https://apify.com/ucchiii/job-postings-normalizer) — Unify job scraper output into one format
- [sitemap-urls](https://apify.com/ucchiii/sitemap-urls) — List website URLs, then dedupe them with this Actor

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
