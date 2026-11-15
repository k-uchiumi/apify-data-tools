# Website Image Extractor: Image URLs and Alt Text

**Run it on Apify:** https://apify.com/ucchiii/website-image-extractor · **Price:** $2.00 per 1,000 page image lists · free demo with empty input

**Website Image Extractor: give it page URLs and get the list of images on each page — image URL, largest srcset image, alt text, width and height attributes, lazy loading, and file size and type — with missing alt text and oversized images flagged. Plain HTTP, no browser, no crawling, and no image is ever downloaded.**

| image | alt | width × height | loading | content-length | content-type | flags |
|---|---|---|---|---|---|---|
| https://apify.com/img/apify-logo/wordmark-white.svg | "Apify logo" | 100 × 27 | — | 2,587 | image/svg+xml |  |
| https://cdn-cms.apify.com/mcp_34048c6902.png | "Promotion image background" | — × — | lazy | 17,191 | image/png |  |
| https://cdn-cms.apify.com/apify_store_dark_bd743acd0f.webp | "Promotion image background" | — × — | lazy | 36,846 | image/webp |  |

*(real rows from a run on https://apify.com/ on 2026-11-12, shortened to 7 of the fields; the dataset has one row per page with the full `images` list and counts)*

**For SEO specialists, web developers, accessibility reviewers, content teams and site owners** who need to see which images a page really uses — to find images with no alt text, to find the heavy files that slow a page down, or to get a list of image URLs before they decide what to save — without opening the browser's developer tools page by page.

**Try it:** press **Start** with the prefilled example (2 pages) and you get 2 rows in a few seconds, for less than a cent. Clear the inputs and press Start for a **free demo** on 2 sample pages (rows carry `_demo: true`, not charged).

## What you get for each image
- **`src`**: the image URL as an absolute URL. If the page uses lazy loading with a placeholder `src` (a `data:` image), the real URL from `data-src` (or `data-lazy-src`, `data-original`) is used.
- **`largestSrcset`** and **`largestSrcsetDescriptor`**: the biggest candidate from `srcset` (`1600w`, `2x`) — also from the `<source>` tags of a `<picture>` — so you know which file a large screen gets.
- **`alt`**, **`altMissing`**, **`altEmpty`**: the alt text. `altMissing` is true when the attribute is absent. `alt=""` is accepted as a decorative image and shows as `altEmpty`, not as missing.
- **`width`, `height`**: the attributes as written in the HTML (numbers only; no CSS is evaluated). Missing values mean the browser cannot reserve space before the image loads.
- **`loading`**, **`lazy`**: the `loading` attribute, and whether the image is lazy-loaded (`loading="lazy"` or a lazy-loading source attribute).
- **`headStatus`, `contentLength`, `contentType`**: from one **HEAD request** to the image URL — file size in bytes and media type. Some servers answer HEAD with an error or without a size; then these stay empty and the image is still listed.
- **`oversized`** and **`flags`**: `oversized` when `contentLength` is above your limit (default 500 KB); `flags` lists `missing_alt` and `oversized` for easy filtering.
- **`count`**, **`inPicture`**: how often the same URL appears on the page (the image is listed once), and whether it sits in a `<picture>`.

Each page row adds the counts: `imagesTotal`, `imagesMissingAlt`, `imagesEmptyAlt`, `imagesOversized`, `imagesLazy`, `dataUriImages`, `largestImageBytes`, `knownImageBytes`.

## How to use
1. Put your page URLs in **URLs**, one per line.
2. Optionally set the **Oversized limit (KB)**, **Max images per page** (default 200) and **Max HEAD requests per page** (default 30; use 0 to skip HEAD and get only URLs and attributes).
3. Press **Start**, or call the Actor from your code:

```bash
curl -X POST "https://api.apify.com/v2/acts/ucchiii~website-image-extractor/run-sync-get-dataset-items?token=<YOUR_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"urls": ["https://apify.com/", "https://docs.apify.com/"], "oversizedKb": 300}'
```

```json
{
  "urls": ["https://apify.com/", "https://docs.apify.com/"],
  "maxPages": 50,
  "maxImagesPerPage": 200,
  "maxHeadRequestsPerPage": 30,
  "oversizedKb": 500,
  "concurrency": 5
}
```

Export the dataset as JSON for the full image lists. To get one row per image in a spreadsheet, flatten the `images` field with [Dataset Transform](https://apify.com/ucchiii/dataset-transform). The **SUMMARY** record counts images, images without alt, oversized images and HEAD requests for the whole run.

## Images are not downloaded
The Actor reads the page's HTML and asks each image's server for its headers with a HEAD request. It never requests the image file itself, so nothing is downloaded or stored, and no image content is in the output. If you need the files, use [Bulk File Downloader](https://apify.com/ucchiii/bulk-file-downloader) with the image URLs from this Actor. Images can be protected by copyright: only save or reuse images you have the right to use.

## Output fields (per page)
- `inputUrl`, `finalUrl`, `statusCode`, `redirectCount`, `contentType`, `truncated`, `fetchedAt`.
- `imagesTotal`, `imagesListed`, `imagesTruncated`, `imageTags`, `dataUriImages`, `imagesMissingAlt`, `imagesEmptyAlt`, `imagesOversized`, `imagesLazy`.
- `headRequests`, `headSkipped`, `largestImageBytes`, `knownImageBytes`, `oversizedKb`.
- `images`: the list described above.
- `extracted`: true when the page was fetched and its image list was made.
- `error`: `null` when the list was made; otherwise `blocked_by_robots_txt`, `robots_txt_unavailable`, `invalid_url`, `blocked_private_address`, `timeout`, `dns_failure`, `connection_error`, `tls_error`, `http_404` (or another `http_<status>`), `too_many_redirects`, `redirect_loop`, `invalid_redirect_url` or `not_html`.

Per image, `headError` can be `blocked_by_robots_txt` (the image host's robots.txt disallows it, so no HEAD was sent), `robots_txt_unavailable`, `blocked_private_address`, `timeout`, `dns_failure`, `connection_error`, `tls_error`, `too_many_redirects` or `invalid_url`.

## Pricing
Pay per event: **$0.002 per page image list** — a page that was fetched and whose images were listed (also when it has none). That is **$2.00 per 1,000 pages** on the free plan; with Store discounts it is $1.80 (BRONZE), $1.60 (SILVER) and **$1.40 per 1,000 (GOLD)**. There is no start fee, and the HEAD requests for images cost nothing extra.

**Not charged:** pages skipped because robots.txt disallows them, 4xx/5xx pages, timeouts and other fetch errors, blocked addresses, non-HTML files, invalid URLs, and the free demo (empty input).

On the Apify Free plan, the $5 of monthly credit covers about 2,500 pages a month. Set a **maximum charge per run** to cap spend: the run stops cleanly when it is reached.

## Polite and safe by design
- **robots.txt is respected** for the pages and for the hosts of the images. Before fetching a page, the Actor reads that site's robots.txt once and follows the rules for its own name (`website-image-extractor`) or `*`. A disallowed page is not fetched and not charged (`error: blocked_by_robots_txt`). A disallowed image gets no HEAD request (`headError: blocked_by_robots_txt`) but stays in the list. If robots.txt cannot be read because of a server error or timeout, that host is not contacted (`robots_txt_unavailable`). A missing robots.txt (404) means no restrictions.
- **Capped and gentle**: at most 2 connections per site at a time and at least 0.5 s between requests to the same site (a robots.txt `Crawl-delay` is honoured up to 5 s); at most 200 HEAD requests per page (default 30), 2,000 pages per run, 10 redirects per page, 3 redirects per image, 3 MB of HTML per page. The same image URL gets one HEAD request per run, even when many pages use it.
- **Never reaches internal networks**: page and image URLs that resolve to internal addresses (localhost, private ranges, link-local such as 169.254.169.254, IPv6 loopback/unique-local) are never contacted — also when a public URL redirects to one.
- The Actor identifies itself in its User-Agent and does not log in or send cookies.

## Limits
- One request per page; links are not followed (no site crawl). To list a whole site's pages first, use [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls).
- HTTP only: images that a page adds with JavaScript after loading, and CSS background images, are not seen. Only `img` tags (and their `picture` sources) are listed.
- File size comes from the `content-length` header of a HEAD answer. Servers that do not send it, or do not support HEAD, give no size. Sizes are of the file as sent without compression, not of what the page weighs after optimisation.
- Only fetch pages you are allowed to access.

## FAQ
**Does it download my images?** No. It only sends HEAD requests (headers, no file) to the image URLs, and reads the page HTML. Use Bulk File Downloader if you want the files.

**Is an empty alt text "missing"?** No. `alt=""` is the correct way to mark a purely decorative image, so it is counted as `imagesEmptyAlt`. Only an `img` with no `alt` attribute is flagged `missing_alt`.

**Why does an image have no size?** The server did not answer the HEAD request with a `content-length` (some CDNs refuse HEAD, or stream the file), the image is over the per-page HEAD cap (`headChecked: false`), its host's robots.txt disallows it, or it is on a private address.

**Do I pay for pages blocked by robots.txt or broken pages?** No. Only rows with `extracted: true` are charged.

<!-- more-tools:start -->
## More tools from the same developer

- [Bulk File Downloader](https://apify.com/ucchiii/bulk-file-downloader) — download files from URL lists or dataset fields into storage, with links, SHA-256 and an optional ZIP
- [Image Converter](https://apify.com/ucchiii/image-converter) — convert images between JPG, PNG, WebP and AVIF, with optional resize
- [SEO Audit Tool](https://apify.com/ucchiii/seo-audit-tool) — on-page SEO audit for URL lists and sitemaps: title, meta description, h1, canonical, noindex, robots.txt, issues and a 0-100 score
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Favicon Extractor](https://apify.com/ucchiii/favicon-extractor) — favicons, Apple touch icons and manifest icons of many websites, with the best icon per site
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
