# JSON to CSV Converter: Nested JSON to One CSV, No Start Fee

**Run it on Apify:** https://apify.com/ucchiii/json-to-csv · **Price:** $0.05 per 1,000 rows · free demo with empty input

**JSON to CSV converter: turn a JSON file, an NDJSON file or an Apify dataset into one clean CSV file with a download link.**

Paste a link to a JSON file (or pick a dataset), press **Start**, and get a single `.csv` file you can open in Excel, Google Sheets, a database or a script. Nested objects become dot columns (`customer.address.city`), arrays are kept as JSON text in one cell, and you decide which columns go in and in what order. You pay per converted row only — no start fee, no monthly rent.

**Try it free:** leave the input empty and press **Start** — 4 built-in sample orders are converted to `sample-orders.csv` (not charged; the summary item carries `_demo: true`).

## What you get

Input (one JSON row):

```json
{"orderId": "A-1001", "customer": {"company": "Northwind Traders", "address": {"city": "Seattle"}}, "total": 1249.5, "paid": true, "tags": ["priority", "gift"]}
```

CSV file:

| orderId | customer.company | customer.address.city | total | paid | tags |
|---|---|---|---|---|---|
| A-1001 | Northwind Traders | Seattle | 1249.5 | true | ["priority", "gift"] |

- **One CSV file** in the run's key-value store, with a signed **download link**.
- **One summary item** in the dataset: file name, download link, rows, columns, size in bytes, and any requested columns that were not found.

## How to use

1. **JSON or NDJSON file URL** — a public link to a JSON array (`[{...}, {...}]`), an object that holds one array (`{"items": [...]}`, typical for API responses) or NDJSON / JSON Lines (one object per line). **Or** pick an **Apify dataset**, for example the results of a scraper run.
2. Optional **Columns** — the columns you want, in the order you want. `customer` keeps every `customer.*` column. A name that is not in the data is still written as an empty column, so every file you make has the same header.
3. Optional: **Delimiter** (comma, semicolon, tab), **UTF-8 BOM for Excel**, **Formula-safe text** (on by default), **Output file name**.

```json
{
  "fileUrl": "https://example.com/exports/orders.json",
  "columns": ["orderId", "customer.company", "total", "tags"],
  "delimiter": "comma",
  "excelBom": true,
  "fileName": "orders-2026-11"
}
```

## Output details

- **Columns** follow the order they first appear in the data, unless you list them in **Columns**.
- **Values**: numbers stay as written (`1249.5`, `-3`), `true`/`false` stay as text, `null` and empty objects or arrays give an empty cell. Arrays are written as JSON text: `["priority", "gift"]`.
- **Formula-safe**: text that starts with `=`, `+`, `@` (or `-` when it is not a number) gets a leading apostrophe, so a value like `=HYPERLINK(...)` never runs in Excel or Google Sheets. Turn it off to keep text byte-for-byte.
- **Line ends** are CRLF (`\r\n`), as RFC 4180 and Excel expect. The file is UTF-8.
- **STATS** record: source, rows, column names, file size, rows charged and notes.

## Limits (cannot be raised by input)

- JSON arrays and NDJSON files up to **300 MB**; a file that is one object wrapping the rows (`{"items": [...]}`) up to **20 MB** — use an array or NDJSON for bigger data.
- **1,000,000 rows** per run, **1,000 columns** in the CSV, CSV file up to **150 MB**. Use **Columns** to keep fewer columns.
- Files must be UTF-8 (the JSON standard). Public files only, no login. Links to internal network addresses are blocked.
- If the maximum charge you set for the run is lower than the number of rows, the run stops **before anything is charged** and tells you how many rows it can afford — you never get a half-finished CSV.

## Pricing

Pay per event: **$0.00005 per converted row** ($0.05 per 1,000 rows), with no start fee. A 2,000-row file costs $0.10. Failed runs, inputs over a limit and the demo are free.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 100,000 rows a month.

## FAQ

**Can I get the rows in the dataset instead of a file?** This Actor makes one CSV file. If you want flat rows in a dataset (to download as CSV, Excel or JSON from Apify yourself, or to feed another Actor), use the Flatten JSON Actor from the same developer.

**My JSON is one big object, not a list.** If it holds exactly one list of objects (like `{"total": 120, "data": [...]}`), that list becomes the rows. Any other object becomes a single row.

**Accents look wrong in Excel.** Turn on **Add a UTF-8 BOM for Excel**. In many European Excel versions also choose the **semicolon** delimiter.

**Does it change my data?** Only the shape: nested names are joined with dots, and (with formula-safe on) risky text gets an apostrophe. Values are not rounded or re-formatted.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Flatten JSON to CSV](https://apify.com/ucchiii/flatten-json) — flatten nested JSON into flat rows with the separator you choose
- [CSV to Excel Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Excel to JSON](https://apify.com/ucchiii/excel-to-json) — turn Excel (XLSX) sheets into JSON rows, or a JSON file into an Excel sheet
- [Dataset Transform](https://apify.com/ucchiii/dataset-transform) — flatten, filter, rename and convert any Apify dataset, export CSV / JSON / JSONL with a link
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
