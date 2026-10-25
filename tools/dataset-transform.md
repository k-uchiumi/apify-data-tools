# Dataset Transform: JSON to CSV, Flatten, Filter, Rename

**Run it on Apify:** https://apify.com/ucchiii/dataset-transform · **Price:** $0.50 per 1,000 rows · free demo with empty input

**For anyone whose scraper output is nested, messy JSON and who needs a clean table — a CSV for Excel or Google Sheets, a flat file for a database, or tidy rows for the next step of a workflow.** Point this Actor at an Apify dataset (or paste rows), say which columns you want, and get **clean rows plus a CSV / JSONL / JSON file with a download link**.
**$0.50 per 1,000 output rows.** Reading, flattening and filtering are free; rows removed by your filters are not charged. No scraping, no external requests, no API keys.

## What it does
- **Flatten nested JSON**: `{"address": {"city": "Austin"}}` becomes the column `address.city` (up to 20 levels, your own separator)
- **Arrays your way**: keep as JSON, join values (`a; b; c`), one column per item (`tags.0`, `tags.1`), first item only, or item count
- **Select, order, omit and rename columns** (selecting `address` keeps every column under it)
- **Convert types**: text to number (`$1,234.50`, `(12)`, `1.234,50`), integer, boolean (`yes/no/true/1`), ISO 8601 date (from ISO text or Unix seconds/milliseconds), or to text
- **Filter rows**: equals, contains, greater than, in a list, exists and more, combined with AND or OR
- **Sort** (numbers first, empty values last), **skip** and **limit**
- **Save a file** next to the dataset: CSV (delimiter, UTF-8 BOM for Excel, formula-injection protection), JSONL or JSON, with a link in the run statistics
- Works on any dataset: Google Maps, jobs, SERP, e-commerce, your own runs

## Input example
```json
{
  "datasetIds": ["WkzbQMuFYuamGv3YF"],
  "select": ["title", "address.city", "rating", "reviews"],
  "rename": {"address.city": "city"},
  "coerce": {"rating": "number", "reviews": "integer"},
  "filters": [{"field": "rating", "op": "gte", "value": 4.5}],
  "sortBy": "reviews",
  "sortOrder": "desc",
  "limit": 500,
  "exportFormat": "csv",
  "csvBom": true
}
```
**Empty input runs a free demo** on sample orders (not charged, rows carry `_demo: true`) so you can see flattening, type conversion, filtering, sorting and the CSV link before using your own data.

## Order of processing
1. Flatten (depth, arrays) and optional text trimming
2. Convert types (`coerce`)
3. Filter rows, then sort
4. Keep / omit columns, then rename, then cut over-long cells
5. Skip and limit; write the dataset rows; save the file

Column names in `coerce`, `filters`, `sortBy`, `select` and `omit` are the **flattened names before renaming** (for example `address.city`).

## Output
- **Dataset**: the transformed rows. Use Apify's own export buttons for Excel/XML/RSS, or the API.
- **File**: saved in the run's key-value store as `<fileName>.csv` (or `.jsonl` / `.json`). The link is in the `STATS` record as `fileUrl`. It uses Apify's signed public link, so you can share it without sharing your token; if no link can be created, the file is still in the key-value store under `fileKey`.
- **STATS** (key-value store):
```json
{
  "rowsRead": 12034, "rowsFilteredOut": 9120, "rowsWritten": 2914,
  "coercionFailures": {"price": 3}, "cellsTruncated": 0,
  "columns": ["title", "city", "rating", "reviews"],
  "fileKey": "export.csv", "fileFormat": "csv", "fileRows": 2914, "fileUrl": "https://api.apify.com/v2/key-value-stores/…/records/export.csv?signature=…"
}
```

## Limits (cannot be raised by input)
- 1,000,000 output rows per run, 1,000 columns per row, 50,000 pasted rows (use a dataset for more)
- Sorting holds matching rows in memory: up to 500,000 rows (the run stops with a clear message, before any charge, if there are more)
- Files are capped at 150 MB (the dataset still gets every row; `fileTruncated` tells you)
- Text cells are cut at 32,000 characters by default (Excel's limit is 32,767); set `maxCellChars` to change or `0` to disable
- Empty rows (all values empty after your column choice) are skipped and not charged
- Rows that are not objects (a plain number or text) become `{"value": …}`

## Use cases
- Turn a Google Maps / jobs / e-commerce scraper dataset into a flat CSV for a spreadsheet
- Keep only the 6 columns your CRM import needs and rename them to its field names
- Convert price and date text into real numbers and ISO dates before loading to a database
- Filter and sort a dataset (top-rated, cheapest, newest) and hand the top 100 to the next Actor

## Pricing
Pay per event: **$0.0005 per output row** ($0.50 per 1,000). Set a maximum charge per run to cap spend; the Actor stops cleanly when it is reached.

## FAQ
**Does it change my source dataset?** No. It only reads it and writes a new dataset.
**Why are my numbers still text?** Add the column to `coerce` as `number` or `integer`. Values that cannot be read become empty and are counted in `coercionFailures`.
**Excel shows broken characters.** Turn on `csvBom`.
**Is the file link private?** Anyone holding the signed link can download the file; the link does not give access to anything else in your account.

<!-- more-tools:start -->
## More tools from the same developer

- [CSV / XLSX Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Dataset Change Detector](https://apify.com/ucchiii/dataset-change-detector) — compare two runs of any scraper: only added, removed and changed items
- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
