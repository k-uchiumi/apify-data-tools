# Dataset Merge & Dedup: Combine Datasets, Remove Duplicates

**Run it on Apify:** https://apify.com/ucchiii/dataset-dedup-merge · **Price:** $0.50 per 1,000 items · free demo with empty input

**For anyone who runs Apify scrapers more than once and ends up with overlapping results.** This Actor **merges any
number of Apify datasets into one clean dataset and removes duplicates** by the fields you choose.
**$0.50 per 1,000 output items**; reading and comparing is free.

- **Dedup by any fields**: `url`, `placeId`, `title + company`, nested fields like `address.city`, or whole items
- **Smart matching**: ignore letter case, extra whitespace, full-width characters; optional URL normalization (http/https, www, trailing slash)
- **Keep first or last** (e.g. "newest run wins")
- **Output unique items or only the duplicates** (to audit what was removed)
- **Pick / omit fields**, optionally tag each item with its source dataset
- Items are processed as a stream (never all loaded at once), but the dedup keys of everything seen so far are kept in memory — comfortable up to a few million items; for larger datasets split the run (keep="last" also holds one position per key)
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

## Use cases
- Combine Google Maps / jobs / e-commerce scraper runs and remove repeated places, jobs or products
- Merge daily runs into one master list keeping the latest version of each record
- Find which records appear in more than one dataset (`output: duplicatesOnly`)

## Pricing
Pay per event: **$0.0005 per output item** ($0.50 per 1,000). Set a max charge per run to cap spend.

<!-- more-tools:start -->
## More tools from the same developer

- [CSV / XLSX Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Dataset Transform](https://apify.com/ucchiii/dataset-transform) — flatten, filter, rename and convert any Apify dataset, export CSV / JSON / JSONL with a link
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Google Maps Leads Cleaner](https://apify.com/ucchiii/maps-leads-cleaner) — dedupe Google Maps scraper results, format phones (E.164), CRM-ready leads
- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
