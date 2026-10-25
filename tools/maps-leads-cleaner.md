# Google Maps Leads Cleaner: Dedupe, Phones, CSV-ready

**Run it on Apify:** https://apify.com/ucchiii/maps-leads-cleaner · **Price:** $0.50 per 1,000 leads · free demo with empty input

**Already scraped Google Maps? Turn the messy results into a clean lead list you can import into your CRM.**
This is **not a scraper**: you give it the dataset from your Google Maps scraper run (for example `compass/crawler-google-places`)
and get back one clean row per business.

**Try it free:** leave the input empty and press **Start** — it cleans a small sample and nothing is charged.

**Before → after** (sample data from the free demo):

| | name | phone | website / domain | duplicates & closed places |
|---|---|---|---|---|
| Before | Sample Coffee Roasters (2 rows) | `(415) 555-0132` / `+1 415-555-0132` | `https://www.example.com/coffee` | closed bakery still in the list |
| After | Sample Coffee Roasters (1 row) | `+14155550132` | `example.com` | removed |

**How to use it:** 1) run your Google Maps scraper, 2) copy the dataset ID (or Console/API URL) from that run, 3) paste it here and press Start.

- **Who it's for:** sales teams, agencies and lead-gen freelancers who scrape Google Maps.
- **Price:** $0.50 per 1,000 clean leads written (1,000 leads cost 50 cents). Duplicates, filtered-out rows and reading are free.
  Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.

## What it does
- **Merge & dedupe** several runs (by place ID, then name + address, then name + phone)
- **Phones in E.164** (`+14155550132`), validated — invalid numbers are flagged, not guessed
- **Website domain** extracted (`example.com`) for enrichment and email-finder tools
- **Opening hours flattened** to one readable column
- **Filters**: drop closed places, keep only leads with a website / valid phone, minimum rating and reviews,
  include or exclude categories
- Flat columns that import straight into HubSpot, Salesforce, Pipedrive, Google Sheets or CSV

Works with the output of popular Google Maps scrapers (e.g. `compass/crawler-google-places`) and other tools that
use similar field names (`title`/`name`, `phone`, `website`, `address`, `placeId`, `totalScore`, `reviewsCount`).

## Use cases
- Prepare a cold-outreach list: only open places with a website and a valid phone
- Merge several city or category scrapes into one deduplicated list
- Export a CRM import file (HubSpot, Salesforce, Pipedrive) or Google Sheets from raw scraper output

## Input
```json
{
  "datasetIds": ["<dataset ID of your Google Maps scraper run>"],
  "requireWebsite": true,
  "minRating": 4.0,
  "categoriesInclude": ["dentist"]
}
```
Leave `datasetIds` empty to see a free demo on sample data.

## Output (one row per lead)
```json
{
  "name": "Sample Dental Clinic", "category": "Dentist", "categories": ["Dentist"],
  "phone": "+442079460018", "phoneRaw": "020 7946 0018", "website": "example.org", "domain": "example.org",
  "address": null, "street": null, "city": "London", "postalCode": null, "state": null, "countryCode": "GB",
  "lat": null, "lng": null, "rating": 4.9, "reviewsCount": 57, "openingHours": "Monday: 9 AM–5 PM; …",
  "isClosed": false, "placeId": "…", "mapsUrl": "https://www.google.com/maps/…"
}
```
Run statistics (`itemsRead`, `duplicates`, `filteredOut`, `written`, `skippedInvalid`) are saved in the key-value store as `STATS`.

## Notes
This Actor only processes datasets you already own; it does not access Google Maps. You are responsible for how the
underlying data was collected and used.

<!-- more-tools:start -->
## More tools from the same developer

- [Google Maps Reviews Analyzer](https://apify.com/ucchiii/reviews-insights) — per-place ratings, trends, owner response rate and top terms from Google Maps reviews
- [Google Maps Market Analyzer](https://apify.com/ucchiii/maps-market-analyzer) — market and competitor tables from Google Maps scraper datasets
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
