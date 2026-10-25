# Free Stock Photo Search: Openverse, Pexels, Unsplash

**Run it on Apify:** https://apify.com/ucchiii/stock-photos-openverse-search · **Price:** $1.00 per 1,000 image result returneds · free demo with empty input

**Search openly licensed and free-to-use images and get one flat row per image: image URL, thumbnail, size, creator, license and a ready-to-paste attribution text.** Built for bloggers, marketers, designers, newsletter and presentation makers, and developers who need legally clear images and do not want to read three different license pages.

- **Openverse** (Creative Commons and public-domain images from many collections): no key needed
- **Pexels** and **Unsplash**: optional, with your own free API key
- Filter by **commercial use**, **modifications allowed** (Openverse) and **orientation**
- Images are **not downloaded**: you get the URLs returned by each API

**Try it free: leave input empty and press Start.** The demo returns 10 Openverse images for "sunset" and is not charged.

Example output (shortened, illustrative):

| source | title | creator | license | attributionText |
|---|---|---|---|---|
| openverse | Mountain lake at dawn | Jane Doe | CC BY-SA 4.0 | "Mountain lake at dawn" by Jane Doe is licensed under CC BY-SA 4.0. |
| pexels | A calm lake | Max Mustermann | Pexels License | Photo by Max Mustermann on Pexels |
| unsplash | blue lake between mountains | Ann Lee | Unsplash License | Photo by Ann Lee on Unsplash |

**Price: $1 per 1,000 images ($0.001 each).** Example: 10 queries x 10 images = 100 rows = $0.10. Failed requests, searches with no results and the demo are free.

## Input
- `queries`: one search per line (up to 50)
- `sources`: `openverse` (default), `pexels`, `unsplash`. Pexels and Unsplash run only when selected **and** the key is given (`pexelsApiKey`, `unsplashAccessKey`, both secret)
- `commercialUseOnly`, `modificationsAllowed`: license filters, Openverse only (Pexels and Unsplash licenses already allow both)
- `orientation`: `any`, `landscape`, `portrait`, `square`
- `maxResultsPerQuery`: 1 to 500, default 10, counted per query and per source
- `unsplashAppName`: name of your Unsplash app, used as `utm_source` in Unsplash links

## Output fields (one row per image)
`source`, `id`, `title`, `imageUrl`, `thumbnailUrl`, `width`, `height`, `pageUrl`, `creator`, `creatorUrl`, `license`, `licenseVersion`, `licenseUrl`, `attributionText`, `query`.
Extras: `attributionHtml` (same text with the required links, HTML-escaped), `provider` (Openverse: the collection, for example flickr), `downloadTrackingUrl` (Unsplash only, see below).

## Attribution and license rules (read before you publish)
- **Openverse**: each image has its own Creative Commons license (or CC0 / Public Domain Mark). `attributionText` is the sentence Openverse generates for the image. Most CC licenses require credit, a link to the license and a note if you changed the image. Check `license` and `licenseUrl` for each image; NC means non-commercial only, ND means no changes.
- **Pexels**: free to use under the Pexels License. Credit is not legally required but the API terms ask you to credit the photographer and link to Pexels where you show API images; `attributionText` and `attributionHtml` do that.
- **Unsplash**: free to use under the Unsplash License. The API guidelines ask you to **hotlink the URLs returned** (`imageUrl`, `thumbnailUrl`: do not rewrite or re-host them) and to credit the photographer and Unsplash with links that carry `utm_source=<your app>&utm_medium=referral`. `creatorUrl`, `pageUrl` and `attributionHtml` already include it. When a photo is actually used or downloaded by your users, call `downloadTrackingUrl` (with your Authorization header) as the Unsplash guidelines require; this Actor does not call it because it downloads nothing.
- You need your own Pexels / Unsplash account and key and you accept their API terms. Unsplash demo apps are limited to 50 requests per hour, Pexels to 200 per hour by default.

## How it works
- Calls the Openverse API (`api.openverse.org/v1/images/`) anonymously, 20 images per call, with a pause of 1.5 s between calls. On HTTP 429 the Actor waits (Retry-After if given, else 10 s, 30 s, 60 s) and retries up to 3 times; if the limit is still active it stops that source and keeps the rows already collected. Anonymous Openverse limits are strict, so very large runs may return fewer images than requested.
- Pexels (`api.pexels.com/v1/search`, up to 80 per call) and Unsplash (`api.unsplash.com/search/photos`, up to 30 per call) use the same pacing and retry rules.
- Your keys are used only for the run and are never logged or written to the output.
- A row is charged only after it is written. If you set a maximum cost per run, the run stops at the limit.

## Price
| Event | Price |
|---|---|
| `image` (one image row written) | $0.001 |

**Not charged:** failed requests, searches with no results, skipped sources and the empty-input demo.

## Notes
Images belong to their creators; the license shown per image is what the source reports. This Actor does not verify licenses and is not legal advice. Pexels and Unsplash are trademarks of their owners; this Actor is not affiliated with them or with Openverse.

<!-- more-tools:start -->
## More tools from the same developer

- [Website Screenshot API](https://apify.com/ucchiii/website-screenshot) — screenshots and PDFs of web pages: full page, mobile, element, no cookie banners
- [OCR Image to Text](https://apify.com/ucchiii/ocr-image-text) — OCR for image URLs and scanned PDFs with built-in Tesseract: text, confidence and line boxes in 8 languages
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
