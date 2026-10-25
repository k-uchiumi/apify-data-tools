# Dataset Change Detector: new, removed, changed items

**Run it on Apify:** https://apify.com/ucchiii/dataset-change-detector · **Price:** $0.50 per 1,000 changes · free demo with empty input

You run the same scraper every day or week. What you actually need is **what changed**: the price that dropped, the
product that went out of stock, the new job posting, the listing that disappeared. This Actor compares the
**previous dataset** with the **current dataset** and writes only the differences — no scraping, no proxies, no
code.

- **Added / removed / changed** items, one row per change
- **Field-level diff**: `field`, `before`, `after` and `deltaPct` (% change for numbers)
- **Smart matching**: match items by `url`, `id`, your own key fields (e.g. `asin`) or, as a last resort, a hash of the item
- **Watch or ignore fields**: track only `price` and `inStock`, ignore noise like `scrapedAt`; nested paths such as `offer.price` work
- **Numeric threshold**: ignore price moves smaller than, say, 1%
- Optionally output `unchanged` items too
- **$0.50 per 1,000 changes.** Reading datasets is free; only the items written are billed.

Typical uses: price monitoring, stock alerts, new job postings, real-estate listing changes, competitor catalog tracking.

## Use cases
- Price and stock monitoring: rerun an e-commerce scraper daily and get only price drops and out-of-stock items
- New and removed job postings between two runs of a job scraper
- Real-estate or marketplace listings that appeared or disappeared since last week
- Alert workflows: send only the changes to Slack, email or a sheet instead of the full dataset

## Input
```json
{
  "previousDatasetId": "<dataset ID of last week's run>",
  "currentDatasetId": "<dataset ID of this week's run>",
  "keyFields": ["url"],
  "watchFields": ["price", "inStock"],
  "ignoreFields": ["scrapedAt"],
  "numericChangeThresholdPct": 1,
  "outputTypes": ["added", "removed", "changed"]
}
```
Leave both dataset fields empty to see a free demo on sample (fictional) product data.

## Output (one row per change)
```json
{
  "changeType": "changed",
  "key": "https://shop.example.com/p/1",
  "changedFields": [{"field": "price", "before": 80, "after": 68, "deltaPct": -15}],
  "current": {"url": "https://shop.example.com/p/1", "name": "Trail Backpack 30L", "price": 68, "inStock": true},
  "previous": {"url": "https://shop.example.com/p/1", "name": "Trail Backpack 30L", "price": 80, "inStock": true}
}
```
- `added`: `previous` is `null`. `removed`: `current` is `null`. `unchanged`: only `current` is filled.
- Run counts (`added`, `removed`, `changed`, `unchanged`, `previousItems`, `currentItems`, `written`) are saved in the key-value store as `STATS`.

## Use it with a schedule
1. Schedule your scraper in Apify (e.g. daily). Each run creates a new default dataset.
2. Create a schedule or an integration (webhook on "run succeeded") that runs this Actor with the **last two** dataset IDs.
   With the Apify API or a small webhook handler you can pass `previousDatasetId` / `currentDatasetId` automatically.
3. Send the result to Slack, email, Google Sheets or your own system with an Apify integration.

Tip: if every run scrapes the same pages, put a stable field (`url`, `id`, `asin`) in `keyFields`.
If an item has no key, items are matched by a hash of their whole content (minus ignored fields), so any edit shows up as one removed and one added item.

## Notes
- **Memory:** the previous dataset is held in memory as a dictionary of key to (hash, item). Memory use grows with the
  size of the *previous* dataset (roughly the size of its JSON); the current dataset is streamed. For multi-million-item
  datasets, choose an Actor memory of several GB or slim the previous dataset down to the fields you need first.
- If the same key appears twice in one dataset, the last previous item and the first current item are used.
- If the charge limit you set for the run is reached, the Actor stops cleanly and `STATS.stoppedAtChargeLimit` is `true`.
- This Actor only reads datasets you already own; it does not access any website. You are responsible for how the
  underlying data was collected and used.

<!-- more-tools:start -->
## More tools from the same developer

- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Dataset Transform](https://apify.com/ucchiii/dataset-transform) — flatten, filter, rename and convert any Apify dataset, export CSV / JSON / JSONL with a link
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Google Maps Leads Cleaner](https://apify.com/ucchiii/maps-leads-cleaner) — dedupe Google Maps scraper results, format phones (E.164), CRM-ready leads
- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
