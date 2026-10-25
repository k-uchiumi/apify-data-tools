# Website Screenshot API: Full Page, PDF, Mobile, No Banners

**Run it on Apify:** https://apify.com/ucchiii/website-screenshot · **Price:** $4.00 per 1,000 screenshots · free demo with empty input

**For SEO teams, QA engineers, marketers and developers who need reliable screenshots of web pages.** Send a list of URLs
and get a **PNG, JPEG, WebP or PDF** of each one, stored in your key-value store with a public link in the dataset.
**$0.004 per screenshot, $0.006 per PDF — and you only pay when the file was stored successfully.** Leave the input empty
for a free demo.

## Use cases
- **Visual monitoring** of landing pages, pricing pages and competitors (run on a schedule, compare images)
- **Open Graph / social preview images** (device `og`, 1200x630)
- **Archiving** pages as evidence or records, as PDF or full-page PNG
- **Responsive QA**: the same URLs at desktop, tablet and mobile (390x844, 3x, mobile user agent) sizes
- **Docs, reports and thumbnails**: capture one element (`#pricing`, `main article`) instead of the whole page

## Features
- Formats: PNG, JPEG, WebP (with quality) and PDF
- Devices: desktop 1440x900 (default), laptop 1366x768, tablet 768x1024, mobile 390x844, og 1200x630, or custom width/height/scale factor
- Full page (height capped at 20,000 px), optional scroll to trigger lazy-loaded images
- Element screenshot with a CSS selector
- Dark mode, wait strategy (`load`, `domcontentloaded`, `networkidle`) and extra delay (up to 10 s)
- **Hide cookie banners**: CSS hides common consent pop-ups (OneTrust, Cookiebot, Didomi, Quantcast, TrustArc, Usercentrics, CookieYes, Osano and generic cookie banners). Nothing is clicked or accepted.
- **Block ads and trackers**: requests to a small built-in list of common ad/tracking domains are blocked (not a complete filter)

## Price
| Event | Price |
|---|---|
| `screenshot` (PNG, JPEG, WebP) | $0.004 |
| `pdf` | $0.006 |

**Not charged on failures:** timeouts, navigation errors, HTTP 4xx/5xx pages, selector not found, blocked or unsupported
URLs, invalid URLs and files that could not be stored. A row is charged only after its file is stored. If you set a
maximum cost per run, the run stops when the limit is reached. The empty-input demo is free.

## Input
```json
{
  "urls": ["https://example.com", "https://www.python.org"],
  "format": "png", "device": "desktop", "fullPage": true, "scrollToLazyLoad": true,
  "hideCookieBanners": true, "blockAds": false, "waitUntil": "load", "delayMs": 0
}
```
Up to 100 URLs per run (extra URLs are ignored with a warning). Other fields: `quality` (JPEG/WebP, default 80), `width`, `height`,
`deviceScaleFactor` (device `custom` only), `darkMode`, `selector`. There is no login, cookie, header or raw-HTML input.

## Output
One dataset row per URL:
```json
{
  "url": "https://example.com", "finalUrl": "https://example.com/", "status": 200, "format": "png",
  "width": 1440, "height": 900, "bytes": 18234,
  "imageUrl": "https://api.apify.com/v2/key-value-stores/<storeId>/records/screenshot-001-1a2b3c4d5e.png",
  "key": "screenshot-001-1a2b3c4d5e.png", "capturedAt": "2026-10-19T09:00:00+00:00", "error": null
}
```
The file itself is stored in the run's default key-value store with the correct content type. `width`/`height` are the
image size in pixels (`null` for PDF). `imageUrl` is a signed link that opens without an API token; anyone who has the link can open that image, so share it only where you want to. Failed rows have `imageUrl: null` and `error` set to one of: `invalid_url`,
`unsupported_domain`, `blocked_private_address`, `timeout`, `navigation_failed`, `http_<status>`, `selector_not_found`,
`invalid_selector`, `capture_failed`, `storage_failed`. Demo rows carry `_demo: true`.

## Limits
- 100 URLs per run, 4 pages at a time overall, at most 2 at a time per host, 45 s per page
- Full-page height capped at 20,000 px; very long pages are cut at the cap
- Public pages only: no login, no cookies, no custom headers, no HTML input. Pages behind a consent wall that CSS cannot hide, or behind bot protection, may capture the wall (or fail)
- Facebook, Instagram, X/Twitter, TikTok, LinkedIn and Threads are refused (`unsupported_domain`)

## Safety
Only `http`/`https` URLs are accepted. Every host is resolved first and **localhost, private ranges, link-local
(169.254.169.254) and other internal addresses are never contacted** — for the URL itself, each redirect hop and every
request the page makes. Known limit: a DNS answer that changes between the check and the connection (DNS rebinding) is not
detected. You are responsible for having the right to capture the pages you submit (site terms, copyright). Files stay in
your own storage; nothing is kept by the developer.

<!-- more-tools:start -->
## More tools from the same developer

- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [RAG Chunker](https://apify.com/ucchiii/rag-chunker) — split scraped pages into chunks for RAG and vector stores
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
