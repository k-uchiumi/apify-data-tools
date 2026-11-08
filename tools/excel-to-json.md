# Excel to JSON (and JSON to Excel): XLSX Sheets to JSON Rows

**Run it on Apify:** https://apify.com/ucchiii/excel-to-json · **Price:** $0.01 per 1,000 rows · free demo with empty input

**Turn XLSX sheets into JSON rows (and JSON back into Excel)**

**Excel to JSON in one step: paste a link to an .xlsx file and get every row as a JSON object, with numbers kept as numbers and dates as ISO text, plus a downloadable .json file.** Give a JSON or NDJSON file instead and you get an Excel sheet back.

**Try it free:** leave the input empty and press **Start** — a built-in 2-sheet Excel sample is converted (not charged, rows carry `_demo: true`).

| Excel row | JSON row |
|---|---|
| `id` 1, `company` Blue Harbor Co, `monthly_usd` 49, `since` 10/01/2025 | `{"id": 1, "company": "Blue Harbor Co", "monthly_usd": 49, "since": "2025-01-10"}` |

- **Price:** $0.01 per 1,000 rows converted, charged per row ($0.00001 a row) — a 300-row sheet costs $0.003, 50,000 rows cost $0.50. Failed runs and the demo are free. Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
- **Big sheets are fine:** sheets are read row by row; a 1,000,000-row sheet converts within the default memory.

## How to use
1. **File URL**: a public link to an `.xlsx` file (Excel to JSON) or a `.json` / `.ndjson` file (JSON to Excel). The direction follows the file type.
2. Optional: **Sheet** (empty = first sheet, `*` = all sheets), **Header row** (default 1), **Output file name**.

```json
{ "fileUrl": "https://example.com/price-list.xlsx", "sheet": "*", "headerRow": 1 }
```

## Excel to JSON
- **One JSON object per row**, keys from the header row. Empty rows are skipped; empty header cells become `column_1`, `column_2`...; repeated names get `_2`, `_3`.
- **Values keep their type**: numbers stay numbers (`49`, `49.5`), `TRUE`/`FALSE` become `true`/`false`, dates become `2025-01-10` and date-times `2025-03-01T14:30:00`, empty cells `null`.
- **Formulas**: the result Excel saved with the file is used, the formula itself is never run (files written by some tools without Excel may have no saved result, which gives `null`).
- **Output**: the rows in the dataset (download as JSON, CSV or Excel from the run page) **and** one `.json` file with a download link — an array of rows, or for all sheets an object keyed by sheet name.

## JSON to Excel
- Accepts a JSON array of objects, an object holding one array (`{"items": [...]}`), or NDJSON (one object per line).
- **Nested objects become dot columns** (`address.city`); arrays are written as JSON text. Columns follow the order they first appear.
- **Safe for Excel**: text is always stored as text, so a value like `=HYPERLINK(...)` never runs as a formula; integers longer than 15 digits are kept as text so Excel does not round them.
- **Output**: one `.xlsx` file with a download link, and a summary item in the dataset.

## Limits (cannot be raised by input)
- Files up to 100 MB; 1,000,000 rows per run; Excel sheets hold at most 1,048,575 rows; cells longer than 32,767 characters are cut (the run tells you how many)
- Old `.xls` files (Excel 97-2003) are not supported — save them as `.xlsx` first. Password-protected files cannot be opened
- Set a maximum charge per run to cap spend; a file that needs more fails before anything is charged. Public files only (no login); links to internal network addresses are blocked

## Pricing
Pay per event: **$0.00001 per row converted** ($0.01 per 1,000 rows), charged per row. Failed runs and the demo are free.
On the Apify Free plan, the $5 of monthly platform credit covers about **500,000 rows** a month — enough to try it on real data.

## FAQ
**CSV files?** Use the CSV / XLSX Converter from the same developer — it handles CSV, TSV, Excel, JSON and NDJSON in any direction.
**Need only some rows?** Filter the JSON rows afterwards with the Filter Rows Actor from the same developer.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [CSV to Excel Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Dataset Splitter](https://apify.com/ucchiii/dataset-splitter) — split a dataset or CSV/JSON file into parts by rows, parts, column value or size, or take a random sample
- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
