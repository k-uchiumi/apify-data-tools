# Dataset Splitter: CSV, XLSX, JSON by rows or column

**Run it on Apify:** https://apify.com/ucchiii/dataset-splitter · **Price:** $0.10 per 1,000 rows · free demo with empty input

**For anyone whose file is too big for the next tool: Google Sheets stops at 10 million cells, a CRM import accepts 10,000 rows at a time, a colleague needs one file per country.** Give this Actor an Apify dataset or a CSV / TSV / JSON / NDJSON file URL, choose how to cut it, and get **one file (or dataset) per part, each with a download link**.
**$0.10 per 1,000 rows processed** (rounded up per run). Failed runs and the demo are free. No scraping, no API keys.

Sample output (150 rows split into parts of 50):

| partIndex | key | rows | bytes | downloadUrl |
|---|---|---|---|---|
| 1 | iris-part-001.csv | 50 | 1,740 | https://api.apify.com/v2/key-value-stores/…/records/iris-part-001.csv?signature=… |
| 2 | iris-part-002.csv | 50 | 1,751 | … |
| 3 | iris-part-003.csv | 50 | 1,760 | … |

**Try it free: leave input empty and press Start.** The demo splits a built-in 25-row sample CSV into parts of 10 rows (not charged, rows carry `_demo: true`).

**Price example:** a 25,000-row file costs 25 units = **$2.50**, whether you make 3 parts or 300. A 150-row file costs 1 unit = $0.10.

## What it does
- **Fixed rows per part**: for example 10,000 rows per file for CRM imports or Google Sheets
- **Fixed number of parts**: split into N files of nearly equal size
- **One part per column value**: one file per country, status or customer, with a cap on the number of parts
- **Approximate size per part**: aim for files of about 5 MB (estimated from the output format, so allow some percent of difference)
- **Random sample** instead of splitting: n rows or a percentage, with a seed so the same sample can be drawn again (rows keep their original order)
- **Output**: CSV, JSON, NDJSON or XLSX files in the key-value store, and/or one new named dataset per part
- **Reads** an Apify dataset (pick it from your runs) or a public file URL; detects the format, the delimiter and the text encoding (UTF-8, Shift_JIS, Windows-1252)
- **Keeps your data as it is**: CSV values stay exactly as in the file (a ZIP code `00123` stays `00123`); turn on `detectNumbers` if you want real numbers in XLSX or JSON. Every part repeats the header row. Text starting with `=`, `+`, `-`, `@` is stored as plain text in XLSX, never as a formula

## Input example
```json
{
  "fileUrl": "https://raw.githubusercontent.com/mwaskom/seaborn-data/master/iris.csv",
  "splitMode": "column",
  "column": "species",
  "outputFormat": "xlsx"
}
```
To split an Apify dataset use `datasetId`. Modes and their fields: `rows` (`rowsPerPart`), `parts` (`partCount`), `column` (`column`, `maxParts`), `size` (`targetSizeMb`), `sample` (`sampleRows` or `samplePercent`, `seed`).

## Output
- **Dataset**: one summary row per part with `partIndex`, `key` (file name in the key-value store), `value` (the column value, for split by column), `rows`, `bytes`, `format`, `downloadUrl` and, when datasets are written, `datasetName` and `datasetId`. In sample mode the row also has the `seed` used. A failed run has one row with `status: failed` and a plain-language `error`.
- **Key-value store**: the part files, named `<source>-part-001.csv`, `<source>-<value>.csv` or `<source>-sample.csv` (change the start with `fileName`). `downloadUrl` is Apify's signed public link, so you can share a file without sharing your token.
- **New datasets** (option `saveToDatasets`): `prefix-001`, `prefix-002`, ... The default prefix is `split-` plus the run ID, so it is always new. Named datasets stay until you delete them; the run stops before writing if one of them already has items.
- **STATS** record: input rows, parts, bytes, 1,000-row units charged, notes and the sample seed.

```json
{
  "partIndex": 1, "status": "ok", "key": "iris-part-001.csv", "value": null,
  "rows": 50, "bytes": 1740, "format": "csv",
  "downloadUrl": "https://api.apify.com/v2/key-value-stores/…/records/iris-part-001.csv?signature=…"
}
```

## Limits (cannot be raised by input)
- 2,000,000 rows and 1,000 columns per run; 500 parts per run; 200 MB per source file
- A run that would break a limit **fails before anything is written and is not charged**; rows are never silently cut. Example: 1,000,000 rows at 1,000 rows per part would need 1,000 parts, so the run asks for at least 2,000 rows per part
- XLSX parts can have at most 1,048,575 rows (Excel's limit); use CSV or smaller parts. A warning is logged when a part is bigger than the Google Sheets limit of 10,000,000 cells
- Source URLs must be public `http` or `https` links. Addresses on internal networks (localhost, private ranges, cloud metadata) are blocked, also after redirects. XLSX and zip files are not read (convert them to CSV first)
- JSON input must be an array of objects (or an object with one such array); nested values are kept as JSON text in CSV and XLSX output and as nested JSON in JSON, NDJSON and datasets
- The whole source is held in memory: for several million rows start the run with 4 GB or more memory

## Pricing
Pay per event: **$0.10 per 1,000 rows processed** (event `rows-1000`). All rows read from the source count once, rounded up per run: 2,500 rows cost 3 units ($0.30), regardless of mode, number of parts or output format (a random sample is charged for the rows read, not for the sample size). Nothing is charged for a failed run or the demo. The charge is made after all parts are saved. Set a maximum charge per run to cap spend: if it is too low for the whole source, the run stops before splitting anything.

## Use cases
- Cut a 600,000-row export into files of 10,000 rows for a CRM or e-mail tool import
- Keep a spreadsheet under Google Sheets' 10 million cell limit
- One Excel file per sales region, country or account manager
- Send a reproducible 5 percent test sample of a large dataset to a colleague or a model
- Break a large scraper dataset into 5 smaller datasets that can be processed in parallel

## FAQ
**Does the order of rows stay the same?** Yes, in every mode (also in the sample). In the rows, parts and size modes the parts are consecutive blocks of the source.
**What happens to rows with an empty value in the split column?** They go to a part named `empty`.
**How exact is the size mode?** It estimates each row's size in the chosen format and fills parts up to the target. CSV, JSON and NDJSON are usually within a few percent; XLSX is compressed, so it is only a rough guide.
**Can I run it twice with the same dataset prefix?** Not if the first run left items in those datasets. Pick a new prefix or delete the old datasets.
**Does it change my source?** No. It only reads the file or dataset and writes new files.
**Is the file link private?** Anyone holding the signed link can download that file; it gives no access to anything else in your account.

<!-- more-tools:start -->
## More tools from the same developer

- [Dataset Transform](https://apify.com/ucchiii/dataset-transform) — flatten, filter, rename and convert any Apify dataset, export CSV / JSON / JSONL with a link
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [CSV / XLSX Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Fuzzy Dedupe](https://apify.com/ucchiii/dataset-fuzzy-dedupe) — find near-duplicate names, addresses, phones and URLs that exact dedup misses
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
