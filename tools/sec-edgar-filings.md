# SEC Filings: EDGAR 10-K, 10-Q, 8-K List by Ticker

**Run it on Apify:** https://apify.com/ucchiii/sec-edgar-filings · **Price:** $0.30 per 1,000 listed filings · free demo with empty input

**SEC EDGAR filings list: every 10-K, 10-Q, 8-K, Form 4 and other filing of a company, by ticker or CIK, from SEC's official JSON**

Enter tickers (or CIKs), optionally pick form types and a date range, and press Start. You get one row per filing with the company, CIK, form, filing date, report period, accession number and direct links to the main document and the filing index on sec.gov — ready for monitoring, research or feeding a document pipeline.

## What you get

| company | ticker | form | filingDate | reportDate | accessionNumber | primaryDocumentUrl | filingIndexUrl |
|---|---|---|---|---|---|---|---|
| Apple Inc. | AAPL | 10-K | 2025-mm-dd | 2025-mm-dd | 0000320193-25-xxxxxx | https://www.sec.gov/Archives/edgar/data/320193/.../aapl-2025....htm | https://www.sec.gov/Archives/edgar/data/320193/.../...-index.htm |
| Apple Inc. | AAPL | 10-Q | 2025-mm-dd | 2025-mm-dd | 0000320193-25-xxxxxx | ... | ... |

*(Illustration of the row layout; your run returns the filings as SEC lists them.)*

**Try it free:** leave tickers and CIKs empty and press **Start** for a demo (not charged; rows carry `_demo: true`).

## How to use
1. **Tickers** (`AAPL`, `MSFT`, `BRK.B`) and/or **CIKs** (`320193`) — up to 100 companies per run.
2. Optional **Form types** (`10-K`, `10-Q`, `8-K`, `4`, `S-1`, `DEF 14A`, ...). Empty = all forms. **Include amendments** also lists `10-K/A` when you ask for `10-K` (on by default).
3. Optional **Filed on or after / on or before** dates, **Max filings per company** (newest first) and **Max filings in total**.

```json
{
  "tickers": ["AAPL", "MSFT", "NVDA"],
  "formTypes": ["10-K", "10-Q", "8-K"],
  "dateFrom": "2024-01-01",
  "maxFilingsPerCompany": 50
}
```

## Output fields
- `company`, `cik` (10 digits), `ticker`
- `form`, `filingDate`, `reportDate` (the period the report covers, when there is one), `acceptanceDateTime`
- `accessionNumber`, `items` (8-K item numbers such as `2.02,9.01`)
- `primaryDocument`, `primaryDocDescription`, `primaryDocumentUrl` — the main document on sec.gov
- `filingIndexUrl` — the filing's index page with all its documents and exhibits

A **STATS** record lists every ticker or CIK with its status (`ok`, `unknown_ticker`, `not_found`, ...) and the number of filings found, plus the filings charged and requests made.

## Limits (cannot be raised by input)
- 100 companies and 10,000 filings per run; 10,000 filings per company at most
- Older filings beyond SEC's most recent list are read from up to 20 extra SEC files per company
- Requests to SEC are spaced to stay under SEC's fair-access limit of 10 per second; temporary errors are retried with a pause
- Set a maximum charge per run to cap spend: the run stops cleanly when it is reached

## Pricing
Pay per event: **$0.0003 per listed filing** ($0.30 per 1,000 filings). No start fee. Unknown tickers, failed requests and the demo are free.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 16,666 filings a month.

## FAQ
**Where does the data come from?** From SEC EDGAR's public JSON: the company submissions API on data.sec.gov and SEC's ticker-to-CIK list. Nothing is scraped from web pages.

**Does it download the filing text?** No. It lists filings with direct links to the documents; open or download them from those links.

**A ticker is reported as unknown.** SEC's ticker list covers currently listed companies. For delisted companies, funds or private issuers, use the CIK (search the company on sec.gov to find it).

**How far back does it go?** As far as EDGAR has electronic filings for the company (for most companies, the 1990s or 2000s onward). Use the date fields to keep runs small.

**Is it investment advice?** No. It lists public filings as SEC publishes them.

**Is it affiliated with the SEC?** No. Data source: U.S. Securities and Exchange Commission, EDGAR. This Actor is not affiliated with or endorsed by the SEC.

<!-- more-tools:start -->
## More tools from the same developer

- [French Companies Search](https://apify.com/ucchiii/france-company-search) — French companies and SIREN/SIRET lookups from the official SIRENE register
- [PDF to Text Converter](https://apify.com/ucchiii/pdf-to-text) — plain text per page from public PDF URLs, plus one .txt file per PDF
- [CSV to Excel Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
