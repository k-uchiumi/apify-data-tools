# Filter CSV, JSON or Dataset Rows by Condition

**Run it on Apify:** https://apify.com/ucchiii/filter-rows · **Price:** $0.01 per 1,000 rows · free demo with empty input

**Keep only the CSV, JSON or dataset rows that match your conditions**

**Filter rows of a CSV, TSV, JSON or NDJSON file or an Apify dataset with plain conditions like `country = US` or `price > 100`.** Paste a file link (or pick a dataset), write one condition per line, press Start: the matching rows land in a dataset you can download as CSV, Excel or JSON.

**Try it free:** leave the file empty and press **Start** — the conditions already in the form run on a built-in 20-row sample (not charged, rows carry `_demo: true`).

| condition | keeps rows where |
|---|---|
| `country = US` | the country is US (text is compared without case) |
| `price > 100` | the price is more than 100 (numbers are compared as numbers) |
| `signup_date >= 2025-06-01` | the date is on or after 1 June 2025 (ISO dates) |
| `email contains @gmail.com` | the email contains that text |
| `phone is empty` | the phone column is blank |
| `status in open, pending` | the status is one of the listed values |

- **Price:** $0.01 per 1,000 rows read, charged per row ($0.00001 a row) — 100,000 rows cost $1, whether 5 or 50,000 of them match. Failed runs and the demo are free. Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
- **Big files are fine:** CSV, TSV and NDJSON are read one row at a time, so a 3-million-row CSV uses about as little memory as a small one.

## How to use
1. **File URL** (a public link to a CSV, TSV, JSON or NDJSON file) **or an Apify dataset** (for example the results of a scraper run).
2. **Conditions**, one per line: `column operator value`.
3. **Keep rows that match**: *all* conditions (AND, default) or *any* condition (OR).

```json
{
  "fileUrl": "https://example.com/leads.csv",
  "conditions": ["country = US", "employees >= 50", "email is not empty"],
  "match": "all"
}
```

## Operators
`=` `!=` `>` `>=` `<` `<=` · `contains` · `not contains` · `starts with` · `ends with` · `in` (comma list) · `not in` · `is empty` · `is not empty`

- Text comparisons ignore case and surrounding spaces. `1,000` and `1000.0` both count as the number 1000.
- `>` and `<` compare numbers as numbers; other text (such as ISO dates `2025-06-01`) is compared alphabetically, which is correct for ISO dates. Blank values never pass `>` or `<`.
- Nested fields of JSON or dataset rows: `address.city = Berlin`.
- A column name with spaces works as is (`Monthly Spend >= 49`); put it in quotes if it contains an operator (`"a=b" = 1`).
- Column names are matched without case. In a CSV, a column that does not exist stops the run **before anything is charged** and lists the real column names.

## Output
- **Dataset**: every matching row with all its original columns and values, in the original order. CSV values stay exactly as in the file (a ZIP code `00123` stays `00123`). Download it as CSV, Excel, JSON, XML or HTML from the run page.
- **STATS** record: rows read, rows matched, the conditions used, the rows charged and whether the run stopped early.

## Limits (cannot be raised by input)
- Files up to 500 MB; JSON arrays up to 150 MB (an array has to be read as a whole — use NDJSON for bigger files); 5,000,000 rows read per run; 20 conditions per run
- Set a maximum charge per run to cap spend: the Actor stops cleanly when it is reached and keeps the rows found so far. `maxOutputRows` stops after that many matching rows
- Public files only (no login). Links to internal network addresses are blocked

## Pricing
Pay per event: **$0.00001 per row read** ($0.01 per 1,000 rows), charged per row. Failed runs and the demo are free.
On the Apify Free plan, the $5 of monthly platform credit covers about **500,000 rows** a month — enough to try it on real data.

## FAQ
**Does it change my data?** No. Rows are copied as they are; only the rows that do not match are left out.
**Excel files?** Convert them first (for example with the Excel to JSON Actor from the same developer), or save the sheet as CSV.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [CSV to Excel Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Dataset Splitter](https://apify.com/ucchiii/dataset-splitter) — split a dataset or CSV/JSON file into parts by rows, parts, column value or size, or take a random sample
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
