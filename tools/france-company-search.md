# France Company Search (SIRENE / SIREN lookup)

**Run it on Apify:** https://apify.com/ucchiii/france-company-search · **Price:** $2.00 per 1,000 company returneds · free demo with empty input

**For anyone who needs facts about French companies in bulk:** sales and market-research teams building lists by sector and postcode, KYC and supplier-onboarding teams checking SIREN / SIRET numbers, data teams enriching a CRM, and journalists or analysts screening a list of names. Give the Actor company names, SIREN / SIRET numbers or filters (postal code, department, NAF activity code, status) and get **one flat row per company: SIREN, name, legal form, NAF code, active or ceased, creation date, headcount band, headquarters SIRET and address, number of establishments**.
**$2 per 1,000 companies.** Data comes from the French government's free **Recherche d'entreprises** API (api.gouv.fr), which serves the **INSEE SIRENE** register. No scraping, no proxies, no API keys. Unofficial: this Actor is not run by INSEE or the French government.

**Try it free: leave input empty and press Start** (a small demo run, not charged, rows carry `_demo: true`).

## Sample output
| siren | name | legalForm | nafCode | status | createdAt | headcount | city | department |
|---|---|---|---|---|---|---|---|---|
| 123456789 | EXEMPLE SAS | SAS, société par actions simplifiée | 62.01Z | active | 2010-05-01 | 20-49 | PARIS | 75 |
| 234567890 | EXEMPLE BOULANGERIE SARL | SARL | 10.71C | ceased | 1998-03-12 | 3-5 | LYON | 69 |
| 345678901 | null (sole proprietor) | Entrepreneur individuel | 47.11B | active | 2019-09-02 | 0 employees | NANTES | 44 |

(Made-up placeholder values that show the shape of a row.)
Example price: 1,000 companies cost $2; 50 companies cost $0.10.

## What it does
- **Search by name or keyword**: each query returns up to `maxResultsPerQuery` companies (prefill 5, cap 1,000)
- **Look up SIREN (9 digits) or SIRET (14 digits) numbers**: spaces and dots are ignored, a SIRET returns its company
- **Filters** that can be combined with queries or used alone: postal code, department, NAF code, NAF section, active / ceased
- **Matching establishments** (SIRET, address, status) for text and filter searches, if you turn it on (default on)
- **Dedupes**: a company found by several queries is returned, and charged, once
- **Polite**: at most 2 requests per second (the API allows about 7), backs off on HTTP 429 and honours `Retry-After`

## Privacy: no officers, no personal names
The API also lists company officers (*dirigeants*). **This Actor never reads or outputs them**, and no personal names of any kind. For sole proprietors (*Entrepreneur individuel*, legal form code 1000) the company name is the person's own name, so `name` is `null` and `isSoleProprietor` is `true`; their street address is dropped too (postal code and city stay). The same applies to companies the register marks as partially non-diffusible.

## Input example
```json
{
  "queries": ["Danone", "boulangerie Lyon"],
  "sirenList": ["552 032 534"],
  "codePostal": "69003",
  "etatAdministratif": "A",
  "maxResultsPerQuery": 5
}
```
Filters apply to every query. With only filters (no query), one search is run for the filters alone.

## Output
One row per company in the dataset (an overview table and a details table are included):
```json
{
  "siren": "123456789",
  "name": "EXEMPLE SAS",
  "isSoleProprietor": false,
  "legalFormCode": "5710",
  "legalForm": "SAS, société par actions simplifiée",
  "nafCode": "62.01Z",
  "nafLabel": null,
  "nafSection": "J",
  "nafSectionLabel": "Information and communication",
  "createdAt": "2010-05-01",
  "status": "active",
  "ceasedAt": null,
  "headcountCode": "12",
  "headcount": "20-49",
  "companyCategory": "PME",
  "hqSiret": "12345678900011",
  "address": "1 RUE EXEMPLE 75002 PARIS",
  "postalCode": "75002",
  "city": "PARIS",
  "department": "75",
  "regionCode": "11",
  "region": "Île-de-France",
  "establishmentsTotal": 3,
  "establishmentsOpen": 2,
  "matchingEstablishments": [{"siret": "12345678900011", "address": "1 RUE EXEMPLE 75002 PARIS", "postalCode": "75002", "city": "PARIS", "status": "active"}],
  "query": "exemple",
  "queriedSiret": null,
  "source": "INSEE SIRENE register via the Recherche d'entreprises API (recherche-entreprises.api.gouv.fr, DINUM / api.gouv.fr). Licence Ouverte / Etalab 2.0."
}
```
Notes: `legalForm` is a label for the most common legal-form codes only (other codes keep the code and a `null` label). `nafLabel` is filled only when the API provides it; `nafSectionLabel` always comes with the section letter. `headcount` is the INSEE employee band at the time of the last declaration and can be out of date. Unknown or invalid SIREN / SIRET numbers and failed requests come back as free rows with an `error` field (`not_found`, `invalid_siren_or_siret`, `rate_limited`, `http_400`, ...).

The record `SUMMARY` in the key-value store counts companies, charged companies, duplicates, not found, invalid, errors and API requests.

## Limits (cannot be raised by input)
- Up to 200 queries, 5,000 SIREN / SIRET numbers, 1,000 results per query and 6,000 API requests per run
- At most 2 requests per second, one at a time; up to 4 retries on 429 / 5xx with growing waits
- The register's search window is 10,000 results per search, and the API returns 25 results per page
- `matchingEstablishments` is filled by the API for text searches and filters, not for plain SIREN lookups

## Pricing
Pay per event: **$0.002 per company returned** ($2 per 1,000). Invalid input, not-found lookups, errors, duplicates and the free demo are not charged. Set a maximum charge per run to cap spend; the Actor stops cleanly when it is reached.

## Data source and licence
Data: INSEE SIRENE register, served by the Recherche d'entreprises API of api.gouv.fr (operated by the French DINUM / Etalab ecosystem), published as open data under the **Licence Ouverte / Open Licence 2.0 (Etalab)**. If you reuse or republish the data, cite the source and the date of extraction, as the licence requires: *"Source: INSEE SIRENE register via api.gouv.fr (Recherche d'entreprises), Licence Ouverte 2.0, extracted <date>"*. Every row carries a `source` field with this attribution. The data may contain personal data of sole proprietors (for example street addresses); if you use it in the EU, GDPR obligations are yours. See `LEGAL.md`.

## FAQ
**Is this official?** No. It is an unofficial client of a free public API, not affiliated with INSEE or the French government.
**Why is there no owner or director name?** By design (see Privacy). The Actor is for company facts, not for finding people.
**Why is `name` null for some rows?** The company is a sole proprietor (*Entrepreneur individuel*) or non-diffusible, and its name would be a person's name.
**How do I get all establishments of a company?** Search with a query or filters and keep `includeEstablishments` on: you get the establishments that matched. A plain SIREN lookup returns the headquarters and the establishment counts.

<!-- more-tools:start -->
## More tools from the same developer

- [Grants.gov Federal Grants Search API](https://apify.com/ucchiii/grants-gov-opportunities) — US federal grant opportunities from Grants.gov: keyword, status, agency and eligibility filters
- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
- [Fuzzy Dedupe](https://apify.com/ucchiii/dataset-fuzzy-dedupe) — find near-duplicate names, addresses, phones and URLs that exact dedup misses
- [CSV / XLSX Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
