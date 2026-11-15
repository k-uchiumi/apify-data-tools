# Google Maps Market Analyzer: area x category, GeoJSON

**Run it on Apify:** https://apify.com/ucchiii/maps-market-analyzer · **Price:** $5.00 per 1,000 segments · free demo with empty input

**Google Maps market analysis: one row per area x category, plus GeoJSON and KML maps**

| area | category | places | avgRating | medianReviews | chainShare | competitionScore |
|---|---|---|---|---|---|---|
| Demo Heights | Cafe | 2 | 4.2 | 31 | 0.5 | 0 |
| Demo Heights | Bakery | 1 | 4.9 | 15 | 0 | 0 |
| Demo Heights | Dentist | 1 | 3.6 | 12 | 0 | 0 |

*(real output of the free demo on 2026-11-04: 14 fictional sample places in 3 areas gave 8 rows like these. The `SUMMARY` record ranked
the areas by competition: Demo Heights 0, Exampleville 99, Sampletown 100, so Demo Heights is the whitespace candidate.)*

You bring the places: this Actor reads the dataset of a [Google Maps Scraper](https://apify.com/compass/crawler-google-places)
(`compass/crawler-google-places`) run and does not scrape Google Maps itself. Pick that run in **Google Maps datasets**
(the field lists the datasets in your account; no ID to copy).

**Try it:** press **Start** with the input as it is (no dataset picked). You get the free demo above: 8 area x category rows,
a `SUMMARY` with the 3 areas ranked, and `MAP_GEOJSON` / `MAP_KML` map files of the 14 sample places, in a few seconds and not charged.
The demo places are fictional; it shows the output format, not a real market.

**Price:** from **$3.50 per 1,000 segments** on Apify's paid plans (the price shown on this page); $5.00 per 1,000 on the free plan.
One segment = one area x category row. Reading, deduplication and map files are free.

Site-selection and sales-territory teams, and agencies building pitch decks, get **a ready-made market analysis from
Google Maps scraper data**: for every area and business category, how many places, how good, how reviewed, how chain-heavy,
the **least-competitive areas**, and **GeoJSON and KML** maps.

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
1. Run a Google Maps scraper (for example [Google Maps Scraper](https://apify.com/compass/crawler-google-places)) for your area and category.
2. Open this Actor and pick that run's dataset in **Google Maps datasets** (the field lists the datasets in your account, so you don't need to copy an ID). You can pick several runs; they are merged and deduplicated.
3. Start. Open the dataset table, the `SUMMARY` record (whitespace candidates) and the `MAP_GEOJSON` / `MAP_KML` records.

**Try it on your own city for a few cents:** scrape one category in one city, run this Actor with **Group areas by = city** (the default), and you get about one segment per category and city name in the data: 10 segments cost 5 cents on the free plan (3.5 cents at the price shown on this page).

Leave **Google Maps datasets** empty to see the **free demo** on fictional sample data (rows are marked `_demo: true`). The demo shows the output format only; the numbers are not real places.

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
| Event | Free plan | Paid plans (Store discount tiers Bronze / Silver / Gold) |
|---|---|---|
| Segment (one area x category output row) | $5.00 per 1,000 | $4.50 / $4.00 / $3.50 per 1,000 |

The price on this page ("from $3.50") is the Gold tier price. A run with 30 areas and 10 categories each produces at most 300 segments
(about $1.50 on the free plan, $1.05 at the Gold tier).
On the Apify Free plan, the $5 of monthly platform credit covers about **1,000 segments** a month — enough to try it on real data.
Set a maximum cost per run in Apify and the Actor stops when it is reached. The demo is free.

## Notes and limits
- The Actor only reads datasets you point it to; you are responsible for how that data was collected.
- Scores and chain detection are estimates from the data you give it; a small or partial scrape gives a partial picture.
- Places without coordinates are left out of `grid` grouping and the map files.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Google Maps Leads Cleaner](https://apify.com/ucchiii/maps-leads-cleaner) — dedupe Google Maps scraper results, format phones (E.164), CRM-ready leads
- [Google Maps Reviews Analyzer](https://apify.com/ucchiii/reviews-insights) — per-place ratings, trends, owner response rate and top terms from Google Maps reviews
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
