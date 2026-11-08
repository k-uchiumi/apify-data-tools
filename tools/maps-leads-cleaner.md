# Google Maps Leads Cleaner: Remove Duplicates

**Run it on Apify:** https://apify.com/ucchiii/maps-leads-cleaner · **Price:** $0.50 per 1,000 leads · free demo with empty input

**Google Maps leads cleaner: turn a messy Google Maps export into a deduplicated, CRM-ready lead list**

**Before → after** (real output of the free demo on 2026-11-04: 4 sample rows in, 2 clean leads out):

| | name | phone | website / domain | duplicates & closed places |
|---|---|---|---|---|
| Before | Sample Coffee Roasters (2 rows) | `(415) 555-0132` / `+1 415-555-0132` | `https://www.example.com/coffee` | closed bakery still in the list |
| After | Sample Coffee Roasters (1 row) | `+14155550132` | `example.com` | removed |
| After | Sample Dental Clinic | `+442079460018` (was `020 7946 0018`) | `example.org` | — |

You bring the places: this Actor reads the dataset of a [Google Maps Scraper](https://apify.com/compass/crawler-google-places)
(`compass/crawler-google-places`) run and does not scrape Google Maps itself. Pick that run in **Google Maps datasets**
(the field lists the datasets in your account; no ID to copy).

**Try it:** press **Start** with the input as it is (no dataset picked). You get the free demo above in a few seconds:
4 sample rows are cleaned into 2 leads (one duplicate merged, one closed place removed), with `STATS` showing the counts. Not charged.

**Price:** from **$0.35 per 1,000 leads** on Apify's paid plans (the price shown on this page); $0.50 per 1,000 on the free plan.
Only clean leads written to the output are charged; duplicates, filtered-out rows and reading are free.

- **Who it's for:** sales teams, agencies and lead-gen freelancers who scrape Google Maps.
- **How to use it:** 1) run your Google Maps scraper, 2) pick that run in **Google Maps datasets** (or paste its dataset ID), 3) press Start.

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
Leave `datasetIds` empty to see the free demo on sample data (2 leads, not charged).

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

## Pricing
| Event | Free plan | Paid plans (Store discount tiers Bronze / Silver / Gold) |
|---|---|---|
| Lead (one cleaned, deduplicated lead written to the output) | $0.50 per 1,000 | $0.45 / $0.40 / $0.35 per 1,000 |

The price on this page ("from $0.35") is the Gold tier price. Failed runs and the demo are free.
On the Apify Free plan, the $5 of monthly platform credit covers about **10,000 leads** a month — enough to try it on real data.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Google Maps Reviews Analyzer](https://apify.com/ucchiii/reviews-insights) — per-place ratings, trends, owner response rate and top terms from Google Maps reviews
- [Google Maps Market Analyzer](https://apify.com/ucchiii/maps-market-analyzer) — market and competitor tables from Google Maps scraper datasets
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
