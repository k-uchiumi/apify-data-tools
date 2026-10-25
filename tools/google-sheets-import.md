# Google Sheets Import: Read Sheet Rows to JSON/CSV

**Run it on Apify:** https://apify.com/ucchiii/google-sheets-import · **Price:** $0.05 per 1,000 rows · free demo with empty input

Turns any Google Sheet tab into an Apify dataset (export as JSON, CSV, Excel or via API) with real numbers, booleans and dates. **Built to just work**: retries on Google rate limits (429) and server errors, and when something is wrong it stops with a plain message ("sheet is not shared", "tab 'X' not found, tabs are: ...") instead of failing silently or returning nothing.

**For** developers, analysts and ops teams who keep input lists, keywords, URLs, prices or leads in a Google Sheet and want to feed them to scrapers, automations or AI pipelines.

- **No setup for public sheets**: share as "Anyone with the link" and paste the URL, no credentials
- **Private sheets**: use your own Google service account (the key is used only in memory and never logged or stored)
- **Clean data**: header row to column names (duplicates and blanks fixed), numbers / booleans / dates converted, empty rows skipped, tab and range selectable

Example output (one dataset item per sheet row):
```json
{ "Student Name": "Alexandra", "Gender": "Female", "Class Level": "4. Senior", "Home State": "CA",
  "Major": "English", "Extracurricular Activity": "Drama Club" }
```

**Price: $0.05 per 1,000 rows** saved (partial thousands round up). Example: a 2,500-row sheet costs 3 x $0.05 = **$0.15**; a 300-row sheet costs $0.05. Header rows, skipped empty rows and failed runs are not charged. Set a max charge per run to cap spend.

**Free demo**: leave the input empty and the Actor reads Google's public "Class Data" sample sheet (10 rows, not charged).

## Input
| Field | What it does |
|---|---|
| `spreadsheet` | Sheet URL or ID. A `#gid=` in the URL selects that tab. |
| `serviceAccountKey` | Optional. JSON key of your service account, for private sheets. |
| `sheetName` / `sheetGid` | Tab by name or by numeric gid. Default: the URL's gid, else the first tab. |
| `range` | A1 range such as `A1:F500`, `A:F`, `2:100`. Default: whole tab. |
| `headerMode`, `skipTopRows` | First row is the header (default) or no header (`column_1`, ...); skip title rows above it. |
| `coerceTypes`, `coerceNumbers`, `coerceBooleans`, `coerceDates`, `dateFormat` | Value conversion. Empty cells are `null`. ZIP-style values with leading zeros and numbers over 15 digits stay text. `03/31/2026`-style dates are converted only if you pick `mdy` or `dmy`. |
| `skipEmptyRows`, `maxRows` | Drop fully blank rows; stop after N data rows (default 10,000). |

```json
{ "spreadsheet": "https://docs.google.com/spreadsheets/d/1AbC.../edit#gid=0", "headerMode": "first", "maxRows": 5000 }
```

## Public sheet in 30 seconds
In Google Sheets click **Share > General access > Anyone with the link > Viewer**, copy the URL into `spreadsheet`, run.

## Private sheet (service account, about 5 minutes)
1. In [Google Cloud Console](https://console.cloud.google.com/) create or pick a project and enable the **Google Sheets API**.
2. **IAM & Admin > Service Accounts > Create service account** (no roles needed), then **Keys > Add key > JSON**.
3. Share your sheet with the account's **`client_email`** as **Viewer**.
4. Paste the whole JSON file into **Service account key**.

## Output
The default dataset has one item per sheet row. Run details are in the key-value store record `RESULT`: source, mode (`public-csv` or `api`), tab, columns, rows in sheet, rows saved, skipped empty rows, `truncatedAtMaxRows` and a `warning` if the tab was empty.

## Common problems
- **"the spreadsheet is not shared publicly"**: set sharing to "Anyone with the link" (Viewer), or use a service account key.
- **"Tab 'X' not found. Tabs in this spreadsheet: ..."** (service account): fix the name or use `sheetGid`.
- **Public sheet and tab by name**: Google's name-based CSV endpoint can drop cells in columns that mix numbers and text. Use `sheetGid` (or the `#gid=` in the URL) for exact values, or use a service account key.
- **403 with a service account**: share the sheet with the `client_email` and check the Sheets API is enabled.
- **"Key creation is disabled"**: many Google Workspace organizations block service account keys (`iam.disableServiceAccountKeyCreation`). Ask your admin, or create the project with a personal Google account.
- **Dates read as text**: only year-first dates (`2026-03-31`) are converted by default; choose `mdy` or `dmy` for slash dates.

## Billing and privacy
You are charged only for rows saved to the dataset. Your key stays in memory for the run and is never written to logs, the dataset or the key-value store. Use a dedicated service account with access only to the sheets you need. The Actor only reads; it never writes to your sheet.

## Pricing
Pay per event: **$0.05 per 1,000 rows** (partial thousands round up). The demo is free.

<!-- more-tools:start -->
## More tools from the same developer

- [CSV / XLSX Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Dataset Transform](https://apify.com/ucchiii/dataset-transform) — flatten, filter, rename and convert any Apify dataset, export CSV / JSON / JSONL with a link
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Dataset Change Detector](https://apify.com/ucchiii/dataset-change-detector) — compare two runs of any scraper: only added, removed and changed items
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
