# Google Maps Market Analyzer: area x category, GeoJSON

**Run it on Apify:** https://apify.com/ucchiii/maps-market-analyzer · **Price:** $5.00 per 1,000 segments · free demo with empty input

Site-selection and sales-territory teams, and agencies building pitch decks, get **a ready-made market analysis from
Google Maps scraper data**: for every area and business category, how many places, how good, how reviewed, how chain-heavy.
Feed it the dataset of a Google Maps scraper run; it finds the **least-competitive areas** and exports **GeoJSON and KML** maps.
**$5 per 1,000 segments** (one segment = one area x category row). Reading, deduplication and map files are free.

- **Three ways to split areas**: by city, by postal code, or by a latitude/longitude grid (`gridKm`, default 2 km)
- **Per area x category**: `places`, `avgRating`, `medianReviews`, `totalReviews`, `closedShare`, `withWebsiteShare`,
  `topPlaces` (top 3 by rating x reviews, with names and Maps URLs), `chainShare`, area centre `lat`/`lng`
- **Chain estimate**: a business name that appears 3 or more times in your data counts as a chain
- **Competition score (estimate, 0-100)** per area from the number of places and total reviews; the **5 weakest areas**
  are listed in `SUMMARY` as whitespace candidates. It is a rough guide, not sales or foot traffic.
- **Map files** of the places (up to the first 100,000 places that have coordinates): `MAP_GEOJSON` and `MAP_KML` (name, category, rating, reviewsCount, mapsUrl) — open in
  QGIS, Google My Maps, Kepler.gl, Felt
- **Privacy**: no phone numbers, no emails, no reviewer or owner names are read or output. The Actor never contacts Google.

Works with the output of `compass/crawler-google-places` and other Maps scrapers using similar field names
(`title`/`name`, `placeId`, `categoryName`/`categories`, `totalScore`, `reviewsCount`, `address`, `city`, `postalCode`,
`location`, `permanentlyClosed`, `website`, `url`).

## Use cases
- **Where to open the next shop or branch**: compare competition and ratings per district for your category
- **Sales territory planning**: find areas with many places but few chains or few websites
- **Agency proposals**: drop a map and a table of local competitors into a client deck

## How to use
1. Run a Google Maps scraper (e.g. *Google Maps Scraper*) for your area and category. Note the dataset ID of the run.
2. Run this Actor with that dataset ID in `datasetIds` (several runs are merged and deduplicated).
3. Open the dataset table, the `SUMMARY` record (whitespace candidates) and the `MAP_GEOJSON` / `MAP_KML` records.

Leave `datasetIds` empty to see a **free demo** on fictional sample data (rows are marked `_demo: true`).

## Input
```json
{
  "datasetIds": ["<dataset ID of your Google Maps scraper run>"],
  "groupBy": "grid",
  "gridKm": 2,
  "categoriesInclude": ["dentist"],
  "minReviews": 5,
  "excludeClosed": true
}
```

## Output (dataset: one row per area x category)
```json
{
  "area": "Sampletown", "category": "Cafe", "lat": 35.06, "lng": 139.09,
  "places": 3, "avgRating": 3.9, "medianReviews": 280, "totalReviews": 730,
  "closedShare": 0, "withWebsiteShare": 1, "chainShare": 1,
  "topPlaces": [{"name": "Example Cafe", "url": "https://www.google.com/maps/place/..."}],
  "competitionScore": 62
}
```
Key-value store: `SUMMARY` (overview, top categories, 5 whitespace candidates), `MAP_GEOJSON`, `MAP_KML`.

## Pricing
**$5 per 1,000 segments.** A run with 30 areas and 10 categories each produces at most 300 segments (about $1.50).
Set a maximum cost per run in Apify and the Actor stops when it is reached. The demo is free.

## Notes and limits
- The Actor only reads datasets you point it to; you are responsible for how that data was collected.
- Scores and chain detection are estimates from the data you give it; a small or partial scrape gives a partial picture.
- Places without coordinates are left out of `grid` grouping and the map files.

<!-- more-tools:start -->
## More tools from the same developer

- [Google Maps Leads Cleaner](https://apify.com/ucchiii/maps-leads-cleaner) — dedupe Google Maps scraper results, format phones (E.164), CRM-ready leads
- [Google Maps Reviews Analyzer](https://apify.com/ucchiii/reviews-insights) — per-place ratings, trends, owner response rate and top terms from Google Maps reviews
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
