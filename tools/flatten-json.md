# Flatten JSON to CSV: Nested to Flat Rows, Any Separator

**Run it on Apify:** https://apify.com/ucchiii/flatten-json · **Price:** $0.15 per 1,000 rows · free demo with empty input

**Flatten JSON: returns one flat row per nested JSON item, with one column per field (`seller.location.city`), ready for CSV, Excel or a database.**

- **Cost:** $0.15 per 1,000 rows, no start fee. **The $5 monthly free credit covers about 33,000 rows.**
- **Output example:** see *What you get* below — one input item, one flat row.

Scrapers and APIs return nested data like `{"seller": {"location": {"city": "Seattle"}}}`. Spreadsheets and SQL tables want flat columns like `seller.location.city`. Point this Actor at one or more **Apify datasets**, a **JSON / NDJSON URL** or **paste JSON**, and every item comes back as one flat row in your dataset — then download it as CSV, Excel, JSON or XML from the run page, or pass it to the next Actor. You choose the key separator, how arrays are written and how deep to flatten. You pay per flattened row only, with no start fee.

**Try it free:** leave the input empty and press **Start** — 3 built-in nested product records are flattened (not charged; rows carry `_demo: true`).

## What you get

Input item:

```json
{"id": 101, "price": {"amount": 49.9, "currency": "USD"},
 "seller": {"name": "Northwind Traders", "location": {"country": "US", "city": "Seattle"}},
 "categories": ["electronics", "accessories"], "variants": [{"color": "black", "stock": 12}]}
```

Flat row (default separator `.`, arrays as JSON text):

| id | price.amount | price.currency | seller.name | seller.location.country | seller.location.city | categories | variants |
|---|---|---|---|---|---|---|---|
| 101 | 49.9 | USD | Northwind Traders | US | Seattle | ["electronics", "accessories"] | [{"color": "black", "stock": 12}] |

With **Key separator** `_` and **Arrays** `join`: `seller_location_city` = `Seattle`, `categories` = `electronics; accessories`.

## How to use

1. **One kind of source**: **Datasets to flatten** (one or more IDs or URLs, for example scrapers' results — with several datasets each row gets `_source` with its dataset ID), **or** a **JSON / NDJSON URL** (a JSON array, an object holding one array like `{"data": [...]}`, or one object per line), **or** **paste JSON** (up to 10,000 items).
2. **Key separator** — `.` (default), `_`, `__`, `/` or any 1–5 characters.
3. **Arrays** — `json` keeps a list as JSON text in one column; `join` writes plain values as `a; b` (set your own **Join separator**). Lists of objects stay JSON text either way, so no data is lost.
4. Optional: **Maximum depth** (deeper parts stay JSON text in one column) and **Stop after this many rows** (to try settings on a big dataset).

```json
{
  "datasetIds": ["https://console.apify.com/storage/datasets/AbCdEf123"],
  "separator": "_",
  "arrays": "join",
  "joinSeparator": ", "
}
```

## Output

- **Dataset**: one flat item per input item, in the original order. Numbers, `true`/`false` and `null` keep their type; empty objects and empty lists become `null`. Items that are not objects (a bare string or number) become `{"value": ...}`.
- **STATS** record: source, separator, rows written and charged, every column name seen, whether the run stopped early, and notes (for example when two keys flatten to the same name — the first value is kept and the clash is counted).

## Limits (cannot be raised by input)

- **1,000,000 rows** per run; JSON / NDJSON files up to **500 MB**; a file that is one object wrapping the rows up to **20 MB**; pasted JSON up to 10,000 items; 2,000 fields per row.
- Public files only, no login. Links to internal network addresses are blocked. Files must be UTF-8.
- Set a maximum cost per run to cap spend: the Actor stops cleanly when it is reached (status Succeeded), keeps the rows written so far and says so in the status message and STATS (`stoppedEarly: "max_charge"`).
- If a file has broken JSON in the middle, the rows before the break are kept (and charged); the rest is skipped and the run says where it stopped.

## Pricing

Pay per event: **$0.00015 per flattened row** ($0.15 per 1,000 rows), with no start fee. Flattening a 10,000-item dataset costs $1.50. Items that cannot be read, failed runs and the demo are free.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 33,000 rows a month.

## FAQ

**Can I get one CSV file with a link instead of dataset rows?** Use the JSON to CSV Actor from the same developer — it writes a single CSV file with a download link and lets you choose the column order.

**Does it split arrays into several rows?** No. This Actor only flattens: one input item gives exactly one output row, so row counts always match your source.

**Why `seller.location.city` and not `seller/location/city`?** That is the default. Set **Key separator** to `/` (or `_` for SQL-friendly names).

**Is my data changed?** Only the shape. Values are copied as they are; nothing is rounded, translated or filtered out.

<!-- more-tools:start -->
## More tools from the same developer

- [JSON to CSV Converter](https://apify.com/ucchiii/json-to-csv) — turn nested JSON files or datasets into one flat CSV file
- [Dataset Explode & Unpivot](https://apify.com/ucchiii/dataset-explode-unpivot) — explode array fields into rows, unpivot wide columns into name/value rows, or pivot back
- [Dataset Transform](https://apify.com/ucchiii/dataset-transform) — flatten, filter, rename and convert any Apify dataset, export CSV / JSON / JSONL with a link
- [CSV to Excel Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
