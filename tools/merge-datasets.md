# Merge Datasets: Combine Datasets, Align Columns

**Run it on Apify:** https://apify.com/ucchiii/merge-datasets · **Price:** $0.30 per 1,000 rows · free demo with empty input

**Returns one dataset with the same columns in every row, merged from several Apify runs (and CSV or JSON files).**

- **Cost:** $0.30 per 1,000 merged rows. **The $5 monthly free credit covers about 16,000 rows.**
- **Output example:** see *What you get* below — two shops with different columns, one aligned table.

Pick the datasets of your scraper runs, optionally add links to CSV, TSV, JSON or NDJSON files, press **Start**: every row of every source lands in one dataset, in order, with one shared set of columns. A column one source lacks is filled with `null`, so the CSV or Excel export lines up. Rename columns that are called differently in different sources (`Title` → `name`), keep only the columns every source shares, and add a `_source` column that says where each row came from.

**Try it free:** leave datasets and files empty and press **Start** — two built-in sample shop lists with different columns are merged (not charged, rows carry `_demo: true`).

## What you get

Two sources with different columns, merged with `renameColumns: {"Title": "name"}` and the source column on:

| name | price | url | inStock | rating | _source |
|---|---|---|---|---|---|
| Ceramic Mug | 12.5 | https://shop-a.example/mug | true | null | demo-shop-a |
| Teak Spoon | 6.9 | https://shop-a.example/spoon | true | null | demo-shop-a |
| Glass Teapot | 31 | https://shop-b.example/teapot | null | 4.7 | demo-shop-b |

- **Every row is kept.** Nothing is de-duplicated: the same row in two sources appears twice. To remove duplicates, use Dataset Merge & Dedup from the same developer.
- **Values stay as they are.** Numbers, booleans, nested objects and arrays from datasets are copied unchanged; CSV values stay text exactly as in the file (`00123` stays `00123`).
- **Big inputs are fine.** Rows are streamed, so a million rows merge within the default memory.

## How to use
1. **Datasets**: pick or paste the datasets to merge (dataset IDs or Apify Console URLs). They are read in the order given.
2. Optional **CSV / JSON file URLs**: public links, merged after the datasets.
3. Optional: **Columns** (`all` or `common`), **Rename columns**, **Add a source column**.

```json
{
  "datasetIds": ["WkzbQMuFYuamGv3YF", "3nQ8cL2xTbYk9VdR1"],
  "fileUrls": ["https://example.com/leads-march.csv"],
  "columns": "all",
  "renameColumns": {"Company Name": "company", "E-mail": "email"},
  "addSourceColumn": true
}
```

## Output
- **Dataset**: the merged rows. Columns are listed in the order they first appear across sources (`_source` last). Download as CSV, Excel, JSON, XML or HTML from the run page.
- **STATS** record: rows per source, the output columns, renamed columns, rows written and charged, and whether the run stopped early.

| field | meaning |
|---|---|
| *your columns* | the union (or the shared set) of the source columns; `null` where a source has no value |
| `_source` | dataset ID or file URL of the row (only with **Add a source column**) |
| `_extra` | extra cells of a CSV line that is longer than its header |

## Limits (cannot be raised by input)
- Up to 10 sources per run (datasets and files together) and 1,000,000 output rows per run
- Files up to 100 MB each; JSON arrays up to 50 MB (an array has to be read whole — use NDJSON for bigger files); up to 2,000 different columns
- Excel files are not read — save them as CSV first. Public files only (no login); links to internal network addresses are blocked
- A source that cannot be read (wrong dataset ID, HTTP error, broken JSON) stops the run **before anything is written or charged**
- Set a maximum cost per run to cap spend: the run stops cleanly at that number of rows (status Succeeded), keeps what was merged and says so in the status message and STATS

## Pricing
Pay per event: **$0.0003 per merged row** written to the output dataset ($0.30 per 1,000 rows). Failed runs and the demo are free.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 16,000 rows a month.

## FAQ
**Does it remove duplicates?** No, by design. Every row of every source is kept. Use Dataset Merge & Dedup if you need duplicates removed by `url`, `email` or other fields.

**My sources call the same column differently.** Use **Rename columns**: `{"Title": "name"}` puts the values of `Title` into `name`. If a row has both, the first non-empty value wins.

**Can I keep only the columns all sources have?** Yes: set **Columns** to `common`. The STATS record notes how many columns were left out.

**Is the order kept?** Yes: sources in the order given (datasets first, then files), rows in their original order.

**Does it work with datasets from other Actors' runs?** Yes, any dataset your account can read. Paste the `defaultDatasetId` of a run, or the dataset URL from Apify Console.

<!-- more-tools:start -->
## More tools from the same developer

- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Filter CSV, JSON or Dataset Rows by Condition](https://apify.com/ucchiii/filter-rows) — keep only the rows of a CSV, JSON file or dataset that match simple conditions like country = US or price > 100
- [CSV to Excel Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Dataset Splitter](https://apify.com/ucchiii/dataset-splitter) — split a dataset or CSV/JSON file into parts by rows, parts, column value or size, or take a random sample
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
