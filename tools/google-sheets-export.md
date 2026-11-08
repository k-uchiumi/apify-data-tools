# Google Sheets Export: Send Scraped Data to a Sheet

**Run it on Apify:** https://apify.com/ucchiii/google-sheets-export · **Price:** $0.05 per 1,000 rows · free demo with empty input

**Google Sheets export: your Apify dataset as rows in a Google Sheet, nested fields flattened into columns**

| name | rating | tags | address.city | address.country | hours.mon | website |
|---|---|---|---|---|---|---|
| Blue Bottle Coffee | 4.6 | coffee; roastery | Oakland | US |  | https://bluebottlecoffee.com/ |
| Stumptown Coffee | 4.4 | coffee | Portland | US |  | https://www.stumptowncoffee.com/ |
| Intelligentsia | 4.5 | coffee; tea; bakery | Chicago | US | 7-18 |  |

*(real output of the free demo on 2026-11-04: the rows and columns that would be written to your sheet; shortened to 7 of the 8 columns)*

To write to your own sheet you need a Google service account key (one-time setup, about 5 minutes; steps below) and the sheet shared with its email.
Pick the dataset in **Datasets** and paste the sheet URL.

**Try it:** press **Start** with the input as it is (no dataset, no sheet). You get the free demo above in a few seconds:
3 sample rows flattened into these columns in the run's dataset, plus a `RESULT` record. Nothing is sent to Google and nothing is charged.

**Price:** from **$0.035 per 1,000 written rows** on Apify's paid plans (shown on this page as "from $0.03"); $0.05 per 1,000 on the free plan.
Charged per row, only after the rows are written.

Writes one or more Apify datasets into a Google Sheet. **Built to just work**: retries on Google rate limits (429) and server errors, clear error messages, and no surprises with nested data.

- **Replace** the tab, or **append** below existing rows (columns are matched to your existing header; new columns are added on the right)
- **Auto columns**: nested objects become `address.city`, arrays become `a; b` (or JSON text)
- **Dedup on append**: skip rows whose key already exists in the sheet
- Creates the tab if it does not exist; cells are capped at Google's 50,000 characters; stops with a clear reason before hitting the 10-million-cell limit
- Your own Google service account; **the key is used only in memory and never logged or stored**
- Set a max charge per run to cap spend.
- Leave `datasetIds` and `spreadsheetId` empty for the **free demo** (3 sample rows in the dataset; nothing is sent to Google)

## Use cases
- Share Google Maps leads or job listings with a sales or recruiting team in a Google Sheet
- Append the results of a scheduled scraper to one sheet every day, skipping rows already there
- Hand cleaned data to non-technical colleagues without CSV downloads

## One-time setup (about 5 minutes)
1. In [Google Cloud Console](https://console.cloud.google.com/), create or pick a project and enable the **Google Sheets API**.
2. Go to **IAM & Admin > Service Accounts > Create service account** (no roles needed).
3. Open the account > **Keys > Add key > Create new key > JSON**. A JSON file is downloaded.
4. Open your Google Sheet > **Share**, paste the service account's **`client_email`** (looks like `name@project.iam.gserviceaccount.com`, found in the JSON) and give it **Editor** access.
5. Paste the whole JSON file into **Service account key** in this Actor's input.

## Input example
```json
{
  "datasetIds": ["aBcDeF123"],
  "spreadsheetId": "https://docs.google.com/spreadsheets/d/1AbC.../edit",
  "serviceAccountKey": "{ ...paste the JSON key... }",
  "sheetName": "Leads",
  "mode": "append",
  "dedupColumn": "url"
}
```

## Output
The default dataset gets one summary item per run; details are in the key-value store record `RESULT`:
```json
{ "spreadsheetUrl": "https://docs.google.com/spreadsheets/d/.../edit#gid=0", "sheetName": "Leads", "mode": "append",
  "rowsWritten": 1200, "columns": ["name", "url", "address.city"], "skippedDuplicates": 35 }
```

## Common problems
- **403 / "share the spreadsheet"**: share the sheet with the `client_email` as Editor (step 4), and check the Sheets API is enabled.
- **"service account key is not valid"**: paste the complete JSON file, including `client_email` and `private_key`.
- **"Key creation is disabled" / no "Add key" option**: many Google Workspace (company) organizations block service account keys by default
  (policy `iam.disableServiceAccountKeyCreation`). Either ask your Google Cloud admin to turn that policy off for one project only,
  or create the project with a personal Google account and share the sheet with its `client_email`.
- **Stopped at the cell limit**: Google allows 10,000,000 cells per spreadsheet. Use `fields` to write fewer columns, or another spreadsheet.

## Billing
You are charged only after rows are written successfully. If Google rejects a write, nothing is charged for those rows.

## Privacy
Your key is kept in memory for the duration of the run only. It is never written to logs, the dataset or the key-value store. Use a dedicated service account with access only to the sheets you need.

## Pricing
| Event | Free plan | Paid plans (Store discount tiers Bronze / Silver / Gold) |
|---|---|---|
| Written row (one row written to the Google Sheet) | $0.05 per 1,000 | $0.045 / $0.04 / $0.035 per 1,000 |

The price on this page ("from $0.03") is the Gold tier price of $0.035, shortened to two decimals. A 300-row export costs 1.5 cents on the free plan.
Reading datasets is free; failed runs and the demo are free.
On the Apify Free plan, the $5 of monthly platform credit covers about **100,000 rows** a month — enough to try it on real data.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Google Sheets Import](https://apify.com/ucchiii/google-sheets-import) — read Google Sheet rows into a dataset as JSON/CSV, public link or your service account
- [Google Maps Leads Cleaner](https://apify.com/ucchiii/maps-leads-cleaner) — dedupe Google Maps scraper results, format phones (E.164), CRM-ready leads
- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Google Maps Reviews Analyzer](https://apify.com/ucchiii/reviews-insights) — per-place ratings, trends, owner response rate and top terms from Google Maps reviews
- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
