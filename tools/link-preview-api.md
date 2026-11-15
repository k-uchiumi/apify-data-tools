# Link Preview API: Open Graph Title, Image & Description

**Run it on Apify:** https://apify.com/ucchiii/link-preview-api · **Price:** $1.30 per 1,000 URLs · free demo with empty input

**Link Preview API: send a list of URLs and get one link-preview row per URL — Open Graph and Twitter Card title, description, absolute image URL, site name, favicon, canonical URL and oEmbed link. Plain HTTP, no browser, built for high-volume link unfurling.**

| inputUrl | title | description | image | siteName |
|---|---|---|---|---|
| https://apify.com | Apify: Marketplace of ready-to-run tools for AI | Thousands of tools to automate your business. Get real-time web data, … | https://apify.com/og-image?title=… | Apify |

*(real output for apify.com on 2026-11-12, shortened to 5 of the columns; the full row also has finalUrl, statusCode, imageWidth/Height (1200 × 630), type, url, favicon, twitterCard, oEmbedUrl, lang and error)*

**For developers who show link previews** — chat apps, CMSs, newsletters, bookmark managers, social schedulers, AI agents — and need the title, description and image of many URLs through one API call, without running a headless browser.

**Try it:** press **Start** with the prefilled example (3 URLs) and you get 3 rows in a few seconds, for less than a cent. Clear the URL list and press Start for a **free demo** on 3 sample URLs (rows carry `_demo: true`, not charged).

## What you get
- **One row per URL, in input order**: `inputUrl`, `finalUrl`, `statusCode`, `title`, `description`, `image`, `imageWidth`, `imageHeight`, `siteName`, `type`, `url` (canonical), `favicon`, `twitterCard`, `oEmbedUrl`, `lang`, `error`.
- **Sensible fallbacks** so you always get the best preview the page offers:
  - title: `og:title` → `twitter:title` → `<title>`;
  - description: `og:description` → `twitter:description` → `<meta name="description">`;
  - image: `og:image` → `og:image:secure_url` → `twitter:image` → `<link rel="image_src">`;
  - canonical: `<link rel="canonical">` → `og:url`; site name: `og:site_name` → `application-name`;
  - favicon: `<link rel="icon">` → `apple-touch-icon` → the site's `/favicon.ico` (not checked);
  - language: `<html lang>` → `Content-Language` → `og:locale`.
- **Absolute URLs**: relative, protocol-relative (`//cdn…`) and `<base href>` image, favicon, canonical and oEmbed URLs are resolved against the final URL. `data:` and `javascript:` URLs are ignored.
- **oEmbed discovery**: the oEmbed endpoint the page advertises (`application/json+oembed`, else XML), ready to call for rich embeds.
- **Any language**: the charset is taken from the HTTP header, a byte-order mark or `<meta charset>`, so Japanese, Cyrillic, Greek or Chinese pages come out right; undeclared legacy pages are read as Windows-1252.
- **Fast and lean**: plain HTTP (no browser), up to 50 URLs in parallel, keep-alive connections, and at most **1 MB of HTML read per page** — reading stops early once the `<head>` has everything.
- **Safe by design**: URLs that resolve to internal addresses (localhost, private ranges, link-local such as 169.254.169.254, IPv6 loopback/unique-local) are never contacted — also when a public URL **redirects** to one. These rows get `error: "blocked_private_address"`.
- **Never fails the whole run on bad input**: DNS failures, timeouts, 404s, non-HTML files and invalid URLs each become a row with an `error` code — and are **not charged**.

## How to use
1. Put your URLs in **URLs**, one per line (up to 1,000 per run).
2. Optional: **Timeout** (default 15 s), **Concurrency** (default 10), **User-Agent**.
3. Press **Start**, or call the Actor from your code. For link unfurling in an app, the synchronous endpoint returns the rows directly:

```bash
curl -X POST "https://api.apify.com/v2/acts/ucchiii~link-preview-api/run-sync-get-dataset-items?token=<YOUR_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"urls": ["https://apify.com", "https://www.python.org"]}'
```

```json
{
  "urls": ["https://apify.com", "https://www.python.org", "https://github.com/apify/crawlee"],
  "timeoutSecs": 15,
  "concurrency": 10
}
```

## Output fields
- `inputUrl` — the URL as given; `finalUrl` — the page that was read after redirects (up to 10); `statusCode` — HTTP status of that page.
- `title`, `description`, `image`, `imageWidth`, `imageHeight`, `siteName`, `type` (`og:type`), `url` (canonical), `favicon`, `twitterCard`, `oEmbedUrl`, `lang` — see the fallbacks above; `null` when the page does not have them.
- `error` — `null` on success, otherwise `invalid_url`, `blocked_private_address`, `timeout`, `dns_failure`, `connection_error`, `tls_error`, `http_404` (or another `http_<status>`), `too_many_redirects`, `redirect_loop`, `invalid_redirect_url`, `not_html` (an image, PDF or JSON) or `no_preview_found` (an HTML page without a title, description or image).
- Also: `hasPreview`, `contentType`, `charset`, `bytesRead`, `truncated` (page longer than 1 MB), `responseTimeMs`, `fetchedAt`.
- **SUMMARY** record: URLs fetched, previews returned, rows charged, errors by code.

## Pricing
Pay per event: **$0.0013 per link preview** — a URL for which a title, description or image was returned. That is **$1.30 per 1,000 previews** on the free plan; with Store discounts it is $1.17 (BRONZE), $1.04 (SILVER) and **$0.91 per 1,000 (GOLD)**.

**Not charged:** rows with an `error` (fetch errors, 4xx/5xx pages, timeouts, blocked addresses, non-HTML files, pages without any preview data), invalid URLs, and the free demo (empty input).

On the Apify Free plan, the $5 of monthly credit covers about 3,800 link previews a month. Set a **maximum charge per run** to cap spend: the run stops cleanly when it is reached.

## Limits
- Up to 1,000 URLs per run (extra URLs are left out and counted in SUMMARY). Duplicates are removed.
- HTTP only: pages that build their title and Open Graph tags with JavaScript after loading, or that block bots, may return little or nothing — try [Website Screenshot](https://apify.com/ucchiii/website-screenshot) for those.
- Only the first 1 MB of HTML is read (Open Graph tags are in the `<head>`, so this is plenty).
- Polite by default: at most 2 connections per host and at least 0.2 s between requests to the same host. Only fetch URLs you are allowed to access.

## FAQ
**Is it fast enough for an app?** For a handful of URLs, `run-sync-get-dataset-items` usually answers within a few seconds, most of it the target sites' own response time. For large lists, run it in batches of up to 1,000.

**Why is the favicon sometimes `/favicon.ico`?** When the page names no icon, browsers fall back to `/favicon.ico` at the site root, and so does this Actor. It is not downloaded or checked.

**Does it follow redirects?** Yes, up to 10, and every hop is checked against private addresses. `finalUrl` shows where it ended; relative image URLs are resolved against that final page.

**Do I pay for broken links?** No. Only rows with a preview are charged.

<!-- more-tools:start -->
## More tools from the same developer

- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Redirect Checker](https://apify.com/ucchiii/redirect-checker) — full redirect chain for each URL, hop by hop, with loop detection
- [Favicon Extractor](https://apify.com/ucchiii/favicon-extractor) — favicons, Apple touch icons and manifest icons of many websites, with the best icon per site
- [Website Screenshot](https://apify.com/ucchiii/website-screenshot) — screenshots and PDFs of web pages: full page, mobile, element, no cookie banners
- [SEO Audit Tool](https://apify.com/ucchiii/seo-audit-tool) — on-page SEO audit for URL lists and sitemaps: title, meta description, h1, canonical, noindex, robots.txt, issues and a 0-100 score
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
