# Dataset Explode & Unpivot: arrays to rows

**Run it on Apify:** https://apify.com/ucchiii/dataset-explode-unpivot · **Price:** $0.20 per 1,000 rows · free demo with empty input

**Arrays to rows, wide to long, long to wide**

**Returns reshaped rows: one row per array element, name/value rows, or one wide row per id.** For anyone whose scraper output has arrays inside rows (reviews, offers, tags, variants) or a spreadsheet layout that does not fit the next tool.** This Actor turns **one row with an array into one row per element**, **wide columns into name/value rows (unpivot)** and **name/value rows back into one wide row per id (pivot)**. It reads Apify datasets or public JSON / CSV file URLs and **works from the Console form**: Apify's own `flatten` and `fields` options for datasets are API-only and not in the Console ([apify-docs issue #3036](https://github.com/apify/apify-docs/issues/3036)), so here you just pick the fields and press Start.

**Try it free: leave input empty and press Start** (clear the prefilled file URL and dataset first; the demo is not charged and rows carry `_demo: true`).
**$0.20 per 1,000 output rows**, charged per row ($0.0002 a row): 1,000 rows cost $0.20, 10,000 rows $2, 100,000 rows $20. **The $5 monthly free credit covers about 25,000 output rows.** Failed runs, runs with no output rows and the demo are free. No scraping, no API keys.

## Sample: explode `reviews`
Input rows: 3 products, 3 reviews in total (one product has none). Output with `explodeFields: ["reviews"]`:

| sku | title | price | _index | reviews.rating | reviews.user.name | reviews.text |
|---|---|---|---|---|---|---|
| KB-01 | Mechanical keyboard | 89 | 0 | 5 | Aiko | Great keys |
| KB-01 | Mechanical keyboard | 89 | 1 | 3 | Ben | A bit loud |
| MS-07 | Wireless mouse | 25.5 | 0 | 4 | Chen | Light and quick |
| HD-22 | USB hub | 18 | | | | |

(The product without reviews is kept as one row with `reviews: null` and an empty `_index`; turn off *keep empty* to drop it.)

## What it does
- **Explode**: one row per element of the array fields you choose. Dot paths work (`reviews`, `offers.items`). The parent fields are copied onto every row (nested objects are flattened with your separator), an **index column** (0-based) says which element it is, and **arrays of objects get their keys flattened with the path as prefix** (`reviews.user.name`). Arrays of plain values land in a column named after the path (`tags`).
- **Several array fields at once**: *zip* pairs elements by position (shorter arrays padded with null), *cross* makes every combination (with one index column per field).
- **Empty arrays**: *keep empty* (default) keeps the parent as one row with null instead of losing it; turn it off to drop such rows. A value that is not an array counts as a single element.
- **Unpivot (wide to long)**: the id columns stay, the value columns (or every other column) become `name` / `value` rows. Rename the two output columns; empty values are dropped by default.
- **Pivot (long to wide)**: id columns + a name column + a value column become one row per id with a column per name. For duplicates choose **first, last, sum, count or list**.
- **Inputs**: Apify datasets (resource picker, read permission only) and public JSON, NDJSON, CSV or TSV file URLs (encoding and delimiter detected, numbers recognized; for a JSON object that wraps the rows give a `recordsPath` such as `products`).
- **Outputs**: the default dataset, plus an optional **CSV file** in the key-value store with a download link (delimiter, UTF-8 BOM for Excel, formula neutralizing).
- **Safe by default**: `maxOutputRows` (default 100,000, maximum 1,000,000) caps run time and cost; when it cuts the output, `truncated` is `true` in the run summary.

## Input example
```json
{
  "fileUrls": ["https://dummyjson.com/products?limit=10&select=title,price,tags,reviews"],
  "recordsPath": "products",
  "operation": "explode",
  "explodeFields": ["reviews"],
  "exportCsv": true
}
```
Unpivot a dataset: `{"datasetIds": ["<id>"], "operation": "unpivot", "idColumns": ["region"], "valueColumns": ["jan","feb","mar"], "unpivotNameColumn": "month", "unpivotValueColumn": "sales"}`.
Pivot it back: `{"datasetIds": ["<id>"], "operation": "pivot", "idColumns": ["region"], "pivotNameColumn": "month", "pivotValueColumn": "sales", "aggregation": "sum"}`.

## Output
- **Dataset**: the reshaped rows. Column names follow the flatten separator (`.` by default). When you use several sources (except pivot, which combines them) each row has a `_source` column.
- **STATS** record (key-value store): `inputRows`, `outputRows`, `columns`, `truncated`, `chargedUnits`, per-source `errors`, counts of skipped rows (`skippedRows` for pivot rows without a name, `rowsWithoutValues` for unpivot, `nonNumericSkipped` for pivot sums) and, with the CSV option, `csvKey` and `csvUrl` (Apify's signed public link).

## Rules worth knowing
- Other nested objects are flattened to dotted columns; other arrays stay arrays in the dataset and become JSON text in the CSV.
- With an empty array the exploded column is the plain path (`reviews`) holding null, because there are no element keys to name.
- Pivot: rows whose name is empty are skipped (counted). `first` and `last` ignore empty values. `sum` adds numbers and numeric text and skips other values (counted). `count` counts rows. A name equal to an id column gets a `_2` suffix. At most 1,000 distinct names.
- Unpivot and pivot use column names as they are after flattening; an id or value column given as a parent name (`customer`) covers every column under it when unpivoting.

## Limits (cannot be raised by input)
- 20 datasets or files per run; 50 MB per file; 1,000,000 input rows and 1,000,000 output rows per run; 50 field names per setting
- Source URLs must be public `http` or `https` links. Addresses on internal networks (localhost, private ranges, cloud metadata) are blocked, also after redirects. Excel files are not read (convert them first)
- The CSV file is capped at 200 MB; the dataset always has every row up to your row limit

## Pricing
Pay per event: **$0.20 per 1,000 output rows** (event `row`, $0.0002 per output row): 2,500 output rows cost $0.50. Input rows are free. Nothing is charged when the run fails or produces no rows. Set a maximum charge per run: the Actor then stops producing rows at that budget (never past it) and says so in `truncated`.
On the Apify Free plan, the $5 of monthly platform credit covers about **25,000 rows** a month — enough to try it on real data.

## Use cases
- One row per review, offer, variant or tag from a scraper dataset, ready for a pivot table or a database
- Wide monthly or per-country columns to a tidy long table for charts and BI tools
- Long exports (id, metric, value) to one wide row per id for a spreadsheet
- Doing from the Console form what the dataset API's `flatten` / `fields` options only do through the API

## FAQ
**Does it change my source dataset?** No. It only reads it and writes a new dataset.
**Why `_index` is empty on some rows?** Those parents had an empty or missing array and were kept because *keep empty* is on.
**My JSON file is an object, not an array.** Give `recordsPath` (for example `data.items`). Without it, the only array of objects in the file is used.
**Can I explode an array inside an array?** Run the Actor twice: explode `orders` first, then explode `orders.items` on the resulting dataset (use the same separator).

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Dataset Transform](https://apify.com/ucchiii/dataset-transform) — flatten, filter, rename and convert any Apify dataset, export CSV / JSON / JSONL with a link
- [CSV to Excel Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Fuzzy Dedupe](https://apify.com/ucchiii/dataset-fuzzy-dedupe) — find near-duplicate names, addresses, phones and URLs that exact dedup misses
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
