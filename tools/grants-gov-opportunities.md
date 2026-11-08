# Grants.gov Federal Grants Search API

**Run it on Apify:** https://apify.com/ucchiii/grants-gov-opportunities · **Price:** $1.50 per 1,000 opportunitys · free demo with empty input

**For grant writers, nonprofits, universities, small businesses, consultants and funding-research teams who need to find US federal grants without clicking through the Grants.gov search page.**
Search by keyword, agency, applicant type and category and get **one flat row per opportunity** with deadline, award amounts and a short synopsis. **$1.50 per 1,000 opportunities**.

**Try it free: leave input empty and press Start.** The demo returns a few current opportunities and is never charged.

| number | title | agency | status | closeDate | awardCeiling |
|---|---|---|---|---|---|
| HHS-2026-ACF-0000 | Community Resilience Program | Administration for Children and Families | posted | 2026-12-15 | 500000 |
| DE-FOA-0003000 | Grid Modernization Projects | Department of Energy | forecasted | 2027-02-01 | 2000000 |

(Illustrative sample. Award fields appear when `includeDetails` is on.)

- **Official API**: uses the public Grants.gov REST API (`search2` and `fetchOpportunity`), no login, no API key, no scraping.
- **Filters**: keyword, statuses (forecasted, posted, closed, archived), agencies, eligibilities (applicant types), funding categories, posted date range.
- **Optional details**: `includeDetails` adds award ceiling and floor, estimated total funding, number of awards, categories, eligibilities and the synopsis.
- **No personal data**: agency contact names, emails and phone numbers are never output.
- **Polite**: one request at a time, at least 1 second apart, identifiable User-Agent, backoff on 429 and 5xx.

Unofficial: **not affiliated with Grants.gov**, the General Services Administration or the US government.

## Input
```json
{
  "keyword": "wildfire",
  "oppStatuses": ["posted", "forecasted"],
  "agencies": ["HHS", "DOE"],
  "eligibilities": ["25"],
  "fundingCategories": ["HL"],
  "postedFrom": "2026-09-01",
  "postedTo": "2026-10-31",
  "includeDetails": true,
  "maxItems": 50
}
```
- `oppStatuses`: any of `forecasted`, `posted`, `closed`, `archived` (empty = forecasted + posted).
- `agencies`, `eligibilities`, `fundingCategories`: Grants.gov codes, one per line (see the field descriptions for common codes).
- `postedFrom` / `postedTo`: `YYYY-MM-DD`, compared with the opportunity's open (posting) date. Applied by the Actor after the search, so a narrow range on a very large result set scans more pages (up to 3,000 search hits per run).
- `maxItems`: 1 to 1,000 (default 10).

## Output (one item per opportunity)
```json
{
  "id": "350000", "number": "HHS-2026-ACF-0000", "title": "Community Resilience Program",
  "agency": "Administration for Children and Families", "agencyCode": "HHS-ACF",
  "status": "posted", "openDate": "2026-09-01", "closeDate": "2026-12-15",
  "awardCeiling": 500000, "awardFloor": 10000, "estimatedTotalFunding": 2000000, "expectedNumberOfAwards": 4,
  "categories": ["Health"], "eligibilities": ["Nonprofits having a 501(c)(3) status with the IRS, other than institutions of higher education"],
  "synopsis": "Funds community projects that ...",
  "url": "https://www.grants.gov/search-results-detail/350000"
}
```
- Without `includeDetails`, award fields, `categories`, `eligibilities` and `synopsis` are empty (the search call does not return them).
- `synopsis` is plain text, cut at 2,000 characters.
- A run summary (total matches, scanned, returned, errors) is saved to the key-value store as `SUMMARY`.

## Pricing
Pay per event: **$0.0015 per opportunity returned** (1,000 opportunities cost $1.50). The empty-input demo and failed runs are free.
On the Apify Free plan, the $5 of monthly platform credit covers about **3,333 opportunities** a month — enough to try it on real data.
`includeDetails` makes one extra API call per opportunity (about 1 second each) but does not cost extra. Set a max charge per run in the run options to cap spend.

## Use cases
- Weekly alert feed of new grants for your sector or applicant type
- Funding pipeline research for nonprofits, universities and startups
- Dashboards of upcoming (forecasted) federal funding by agency
- Enriching CRM or newsletter content with grant deadlines

## Notes
- Data comes only from the public Grants.gov API. Grants.gov data is US government information; check the Grants.gov terms for reuse.
- Always confirm deadlines and eligibility on the linked Grants.gov page before applying.
- Not affiliated with Grants.gov.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [French Companies Search](https://apify.com/ucchiii/france-company-search) — French companies and SIREN/SIRET lookups from the official SIRENE register
- [CSV to Excel Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Dataset Change Detector](https://apify.com/ucchiii/dataset-change-detector) — compare two runs of any scraper: only added, removed and changed items
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
