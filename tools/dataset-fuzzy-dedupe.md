# Fuzzy Dedupe: Fuzzy Matching for Near-Duplicate Rows

**Run it on Apify:** https://apify.com/ucchiii/dataset-fuzzy-dedupe · **Price:** $0.50 per 1,000 items · free demo with empty input

**Find near-duplicate rows that exact dedup misses**

**For anyone with scraped or exported business lists** (leads, places, products, suppliers) where the same company
appears as "Acme Inc." and "ACME, Inc", or "12 Main St." and "12 Main Street". Exact dedup keeps both. This Actor
**finds the near-duplicates across one or more Apify datasets, groups them into clusters, and either marks every row
or merges each cluster into its most complete row**, with a score and a plain-text reason for every decision.

**Try it free: leave input empty and press Start** (sample rows, rows carry `_demo: true`, nothing is charged).

## Sample output (mark mode)

| company | clusterId | isPrimary | matchScore | matchReason |
|---|---|---|---|---|
| Acme Inc. | c1 | true | 1.0 | primary: most complete row of 2 near-duplicates |
| ACME, Inc | c1 | false | 1.0 | confirmed (4 fields agree, score 1.00): company exact; address exact; phone exact; website exact |
| Northwind Traders LLC | c3 | true | 1.0 | unique |
| Delta Dental Clinic | c10 | true | 0.96 | possible duplicate of demo:10 (best score 0.96) but not safe to auto-merge |

**Pricing:** $0.50 per 1,000 input rows processed ($0.0005 each). Example: 1,000 rows cost $0.50, 20,000 rows cost $10. **The $5 monthly free credit covers about 10,000 rows.**
Merge mode charges for the input rows too (every row that went into a merged row), not only the rows written.
Failures, errors and the demo are free. Set a max charge per run to cap spend.

## What it does
- **Normalization**: case, punctuation, accents, legal suffixes (Inc, LLC, Ltd, Corp, GmbH, AG, S.A., 株式会社, 有限会社 ...), street abbreviations (St/Street, Ave, Rd, Hauptstr./Hauptstrasse, N/North ...), ordinal numbers, full-width/half-width characters, Japanese address units (1丁目2番3号 = 1-2-3), phone digits (country code and formatting ignored), URL host (www, scheme and path ignored).
- **Similarity** (via [rapidfuzz](https://github.com/rapidfuzz/RapidFuzz) 3.14.3): for names and addresses the average of Jaro-Winkler and token-sort ratio; for free text token-sort ratio; phones and URL hosts are exact. Rows whose numbers differ ("Store 12" vs "Store 21", "12 Main St" vs "21 Main St") are penalized.
- **Several fields, weighted**: each field has a `type` (`name`, `address`, `phone`, `url`, `text`) and a `weight`. The row score is the weighted average over the fields both rows have.
- **Blocking** to stay fast: only rows with the same key (first letters, postal code, or website domain) are compared. Huge blocks are compared with a sliding window.
- **Two modes**: `mark` keeps every row and adds `clusterId`, `isPrimary`, `matchScore`, `matchReason` (plus `matchStatus`, `clusterSize`, `rowId`, `primaryRowId`, `possibleDuplicateOf`). `merge` writes one row per cluster: the most complete row plus `mergedRowIds`.
- **Safety rule**: rows are merged into a cluster only when the weighted score is at least the threshold **and** either at least two independent fields agree, or a strong field (phone, URL host, or the only field you configured) agrees. Everything else that looks similar is only reported as `possible` (`possibleDuplicateOf`) and is never merged.
- **Memory-conscious**: rows are streamed; only compact normalized values are kept in memory, not the full rows. Up to **200,000 rows per run** (more are ignored and not charged) and 20 datasets.

## Input example
```json
{
  "datasetIds": ["aBcDeF123"],
  "matchFields": [
    {"field": "name", "type": "name", "weight": 2},
    {"field": "address", "type": "address", "weight": 1},
    {"field": "phone", "type": "phone", "weight": 1}
  ],
  "threshold": 0.9,
  "mode": "mark",
  "blockingMethod": "firstLetters"
}
```
Run statistics (rows read, clusters, duplicates, possible matches, rows charged) are saved in the key-value store as `STATS`.

## Sample `matchReason` values
- `confirmed (4 fields agree, score 1.00): company exact ('acme' ~ 'acme'); address exact ('12 main street springfield 62701' ~ '12 main street springfield 62701'); phone exact; website exact`
- `confirmed (strong field agrees, score 0.94): name 0.88 ('globex corporation' ~ 'globex corp'); phone exact`
- `possible duplicate of demo:10 (best score 0.96) but not safe to auto-merge: fewer than two independent fields agree and no strong field (phone, url) matches`
- `linked through other similar rows; directly vs primary: ...` (A matches B and B matches C, but A and C only through B: check these)

## Disclaimer
**Fuzzy matching can be wrong.** Different companies can have similar names, and one company can legitimately have several
addresses or phone numbers. Treat results as suggestions: start with `mark` mode, review `matchReason`,
`possibleDuplicateOf` and low `matchScore` rows, and keep your source datasets. Your source datasets are never
changed, but the merged output only keeps the most complete row of each cluster.

## Tips
- Use a higher threshold (0.93-0.97) for names alone, lower (0.85) when phone or website also match.
- Blocking hides matches whose key differs (a typo in the first letters, a different postal code). For maximum recall on small data use `blockingMethod: none` (comfortable up to roughly 20,000 rows; above that it falls back to a sliding window).
- Rows with an empty blocking value are only compared with each other.

## Pricing
Pay per event: **$0.0005 per row processed**. Failed runs and the demo are free. Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about **10,000 rows** a month — enough to try it on real data.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Google Maps Leads Cleaner](https://apify.com/ucchiii/maps-leads-cleaner) — dedupe Google Maps scraper results, format phones (E.164), CRM-ready leads
- [CSV to Excel Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Dataset Transform](https://apify.com/ucchiii/dataset-transform) — flatten, filter, rename and convert any Apify dataset, export CSV / JSON / JSONL with a link
- [Japan Address Normalizer](https://apify.com/ucchiii/japan-address-normalizer) — split Japanese addresses into prefecture, city, town and rest, with coordinates
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
