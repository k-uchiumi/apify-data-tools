# Google Maps Leads Cleaner: Dedupe, Phones, CSV-ready

**Run it on Apify:** https://apify.com/ucchiii/maps-leads-cleaner · **Price:** $0.50 per 1,000 leads · free demo with empty input

**For lead-generation agencies and sales teams:** Google Maps scraper results contain duplicates across runs, phone numbers in many formats and nested fields your CRM cannot import. This Actor turns them into a clean, flat lead list (deduped, E.164 phones, website domains, filters). **$0.50 per 1,000 leads.**

You scraped Google Maps. Now you have **duplicates across runs, phone numbers in five formats, closed businesses and
nested fields** that your CRM won't import. This Actor turns the output of any Google Maps scraper into a **clean,
flat lead list** in one run — no scraping, no proxies.

- **Merge & dedupe** several runs (by place ID, then name + address, then name + phone)
- **Phones in E.164** (`+14155550132`), validated — invalid numbers are flagged, not guessed
- **Website domain** extracted (`example.com`) for enrichment and email-finder tools
- **Opening hours flattened** to one readable column
- **Filters**: drop closed places, keep only leads with a website / valid phone, minimum rating and reviews,
  include or exclude categories
- Flat columns that import straight into HubSpot, Salesforce, Pipedrive, Google Sheets or CSV
- **$0.50 per 1,000 leads written.** Reading, duplicates and filtered-out rows are free.

Works with the output of popular Google Maps scrapers (e.g. `compass/crawler-google-places`) and other tools that
use similar field names (`title`/`name`, `phone`, `website`, `address`, `placeId`, `totalScore`, `reviewsCount`).

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
Run statistics (`itemsRead`, `duplicates`, `filteredOut`, `written`) are saved in the key-value store as `STATS`.

## Notes
This Actor only processes datasets you already own; it does not access Google Maps. You are responsible for how the
underlying data was collected and used.

## Related tools
- [reviews-insights](https://apify.com/ucchiii/reviews-insights) — Analyze the reviews of the same places: ratings, trends, top terms
- [dataset-dedup-merge](https://apify.com/ucchiii/dataset-dedup-merge) — Merge and dedupe any datasets by custom fields
- [url-status-checker](https://apify.com/ucchiii/url-status-checker) — Check that the lead websites still respond

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
