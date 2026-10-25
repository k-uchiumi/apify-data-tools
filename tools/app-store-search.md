# App Store Scraper: Search, Lookup & Top Charts (iTunes API)

**Run it on Apify:** https://apify.com/ucchiii/app-store-search · **Price:** $2.00 per 1,000 app returneds · free demo with empty input

**Get one flat row per iPhone/iPad app from Apple's official iTunes Search API: rating, price, version, size, genres, developer and more.** Built for ASO and market researchers, app developers watching competitors, investors and analysts, and anyone who needs App Store data in a spreadsheet.

- **Search** the App Store by keyword in any country (up to 200 apps)
- **Look up** specific apps by numeric ID, bundle ID or App Store URL
- **Top charts**: current top free or top paid apps per country (up to 100)

Example output row (shortened, illustrative):
```json
{
  "rank": 1, "trackId": 284882215, "bundleId": "com.facebook.Facebook", "name": "Facebook",
  "seller": "Meta Platforms, Inc.", "primaryGenre": "Social Networking", "price": 0.0, "currency": "USD",
  "ratingAverage": 4.3, "ratingCount": 1234567, "version": "480.0", "currentVersionDate": "2026-10-15T07:00:00Z",
  "sizeBytes": 280000000, "minimumOsVersion": "16.0", "contentRating": "12+",
  "url": "https://apps.apple.com/us/app/facebook/id284882215?uo=4", "country": "us"
}
```

**Price: $2 per 1,000 apps ($0.002 each).** Example: a top-100 chart costs $0.20; 50 search results cost $0.10. Failed requests and searches with no results are free. **Free demo:** leave the input empty to get the top 10 free apps in the US at no charge.

## What you get (one row per app)
| Column | Content |
|---|---|
| `trackId`, `bundleId`, `name`, `url` | app ID, bundle ID, title, App Store link |
| `seller`, `developer`, `developerId` | company name, developer name and ID |
| `primaryGenre`, `genres` | main category and all categories |
| `price`, `formattedPrice`, `currency` | price in the storefront of the chosen country |
| `ratingAverage`, `ratingCount` | average rating and number of ratings in that country (plus current-version values when Apple provides them) |
| `version`, `releaseDate`, `currentVersionDate` | latest version, first release, latest update |
| `sizeBytes`, `minimumOsVersion`, `contentRating`, `languages` | file size, minimum iOS, age rating, ISO language codes |
| `rank` | chart position (top charts mode only) |
| `description`, `screenshotUrls`, `ipadScreenshotUrls` | optional, switched on with the input flags |
| `country` | the storefront the row comes from |

## Input
- `mode`: `search`, `lookup` or `topCharts`
- `term` (search), `appIds` (lookup: IDs, bundle IDs or URLs, mixed is fine), `chart` (`top-free` / `top-paid`)
- `country` (two-letter code, default `us`), `limit` (default 50)
- `includeDescription`, `includeScreenshots` (both default off)

Lookup by URL uses the country in the link, for example `https://apps.apple.com/de/app/.../id284882215` returns the German storefront.

## What is not included
**No user reviews.** Apple's public review feed no longer returns data, so this Actor does not provide reviews or review text. It gives app-level rating averages and counts only.

## How it works
- Data comes from Apple's public **iTunes Search API** (`itunes.apple.com/search` and `/lookup`) and Apple's public **RSS top charts** (`rss.applemarketingtools.com`). No login, no API key, no scraping of app pages.
- Apple documents roughly **20 calls per minute**. The Actor waits at least 3 seconds between calls and retries HTTP 403/429 with a growing pause (10 s, 30 s, 60 s). A top-100 chart needs 3 calls, so runs take seconds to a minute.
- Ratings, prices and rankings differ per country; run once per country you care about.
- Chart and search order follow what Apple returns at the time of the run.

## Price
| Event | Price |
|---|---|
| `app` (one app row written) | $0.002 |

**Not charged:** failed requests, searches or lookups with no result, and the empty-input demo. A row is charged only after it is written to the dataset. If you set a maximum cost per run, the run stops when the limit is reached.

## Notes
App Store data belongs to Apple and the app publishers; check Apple's terms for how you may use it. Only app-level public metadata is returned, no personal data.

<!-- more-tools:start -->
## More tools from the same developer

- [Website Technology Detector](https://apify.com/ucchiii/website-technology-detector) — tech stack of websites in bulk: CMS, frameworks, analytics and tag IDs, CDN, consent tools
- [Keyword Research](https://apify.com/ucchiii/keyword-search-volume) — Google Ads search volume, CPC and competition for keyword lists (bring your own DataForSEO key)
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [RSS Feed Reader & Aggregator](https://apify.com/ucchiii/rss-feed-reader) — many RSS / Atom feeds merged into one clean article list
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
