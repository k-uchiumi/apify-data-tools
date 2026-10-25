# CSV / XLSX Converter: CSV, TSV, JSON, NDJSON

**Run it on Apify:** https://apify.com/ucchiii/csv-xlsx-converter · **Price:** $0.05 per 1,000 rows · free demo with empty input

**For anyone who gets a file in the wrong format: a CSV that Excel shows as garbage, an Excel sheet a script cannot read, a JSON export you need as a table, or a scraper dataset that has to become a spreadsheet.** Give this Actor file URLs (or an Apify dataset), pick the output format, and get **the converted file plus a public download link**, with encoding, delimiter and nested JSON handled for you.
**$0.05 per 1,000 rows converted** (rounded up per file). Failed files and the demo are free. No scraping, no API keys.

## What it does
- **Converts between CSV, TSV, XLSX, JSON and NDJSON**: any of them in, any of them out
- **Detects the input format** from the file extension, content type or content; you can also set it
- **Detects the text encoding**: UTF-8 (with or without BOM), Shift_JIS and Windows-1252 (cp1252); or choose one. Output is always UTF-8
- **Detects the delimiter** (comma, semicolon, tab, pipe) or lets you set it; choose the CSV output delimiter and an optional UTF-8 BOM so Excel opens non-English text correctly
- **Reads a chosen XLSX sheet** by name (first sheet by default) and tells you the sheet names if yours is missing
- **Flattens nested JSON** into dot-key columns (`address.city`); arrays stay as JSON text or are joined (`a; b; c`)
- **Selects and renames columns** (selecting `address` keeps every column under it)
- **Turns CSV numbers into real numbers** (so XLSX and JSON get numbers, not text); ZIP codes with leading zeros stay text
- **Safe by default**: text starting with `=`, `+`, `-`, `@` is stored as plain text in XLSX, never as a formula
- Several files per run; each file gets its own output file and one summary row

## Input example
```json
{
  "fileUrls": ["https://raw.githubusercontent.com/mwaskom/seaborn-data/master/iris.csv"],
  "outputFormat": "xlsx",
  "select": ["species", "sepal_length", "petal_length"],
  "rename": {"sepal_length": "sepalLength"}
}
```
To convert an Apify dataset instead, use `datasetIds` (pick it from your runs). **Empty input runs a free demo** on a built-in sample CSV (not charged, rows carry `_demo: true`).

## Output
- **Dataset**: one row per source file with `status`, `inputFormat`, `outputFormat`, `encoding`, `rows`, `columns`, `columnNames`, `outputKey`, `outputUrl` and, for failures, a plain-language `error`.
- **Key-value store**: the converted file, saved under `outputKey` (for example `iris.xlsx`). `outputUrl` is Apify's signed public link, so you can share the file without sharing your token.
- **STATS** record: files converted and failed, rows converted and the number of 1,000-row units charged.

```json
{
  "source": "https://raw.githubusercontent.com/mwaskom/seaborn-data/master/iris.csv",
  "status": "ok", "inputFormat": "csv", "encoding": "utf-8", "outputFormat": "xlsx",
  "rows": 150, "columns": 3, "columnNames": ["species", "sepalLength", "petal_length"],
  "outputKey": "iris.xlsx", "outputBytes": 7312, "chargedUnits": 1,
  "outputUrl": "https://api.apify.com/v2/key-value-stores/…/records/iris.xlsx?signature=…"
}
```

## Limits (cannot be raised by input)
- 50 MB per source file; 50 files or datasets per run
- 500,000 data rows per file or dataset, 1,000 columns, 100 MB per output file. A file over a limit **fails with a clear message and is not charged**; rows are never silently cut
- XLSX input: `.xlsx` / `.xlsm` only (not old `.xls`, not password-protected). Formulas are read as their last saved values
- Source URLs must be public `http` or `https` links. Addresses on internal networks (localhost, private ranges, cloud metadata) are blocked, also after redirects
- Empty rows are skipped; the first non-empty row is the header (duplicate or empty header names become `name_2`, `column_3`)

## Pricing
Pay per event: **$0.05 per 1,000 rows converted** (event `rows-1000`). Rows are counted per file and rounded up: a 2,500-row file costs 3 units ($0.15), a 150-row file 1 unit ($0.05). A file that fails (download error, bad format, over a limit) is not charged. Set a maximum charge per run to cap spend; the Actor stops cleanly before the next file when it is reached.

## Use cases
- Re-save a Shift_JIS or Windows-1252 CSV as UTF-8 so Excel, Sheets and databases show the text correctly
- Turn a CSV export into an XLSX workbook with real numbers, or an XLSX sheet into NDJSON for a script
- Convert a nested JSON API export into a flat table for a spreadsheet
- Hand a scraper dataset to a colleague as an Excel file with a download link

## FAQ
**Which encoding does `auto` choose?** UTF-8 first. If that fails, Shift_JIS when the text contains Japanese kana, otherwise Windows-1252. The detected encoding is in the result row; set `encoding` if it guesses wrong.
**Why is my ID still text?** Numbers with leading zeros, more than 15 digits or exponents stay text on purpose. Turn `inferTypes` off to keep every CSV value as text.
**Excel shows broken characters in my CSV.** Turn on `csvBom`.
**Does it change my source?** No. It only reads the file or dataset and writes a new file.
**Is the file link private?** Anyone holding the signed link can download that file; it gives no access to anything else in your account.

<!-- more-tools:start -->
## More tools from the same developer

- [Dataset Transform](https://apify.com/ucchiii/dataset-transform) — flatten, filter, rename and convert any Apify dataset, export CSV / JSON / JSONL with a link
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Google Sheets Import](https://apify.com/ucchiii/google-sheets-import) — read Google Sheet rows into a dataset as JSON/CSV, public link or your service account
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [PDF Extractor](https://apify.com/ucchiii/pdf-extractor) — PDF to text, tables and metadata from public PDF URLs, page by page
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
