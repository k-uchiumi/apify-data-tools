# Bulk File Downloader: URL List to Storage & ZIP

**Run it on Apify:** https://apify.com/ucchiii/bulk-file-downloader · **Price:** $1.30 per 1,000 saved files · free demo with empty input

**Bulk file downloader: give it a list of file URLs (or point it at a field of another Actor's dataset) and every file is saved to Apify storage, with one row per file — a direct download link, content type, size, SHA-256 checksum and status. Optionally, everything is packed into one ZIP.**

| url | status | fileName | contentType | bytes | publicUrl |
|---|---|---|---|---|---|
| https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf | stored | dummy.pdf | application/pdf | 13264 | https://api.apify.com/v2/key-value-stores/…/records/00001-dummy.pdf |
| https://www.python.org/static/img/python-logo.png | stored | python-logo.png | image/png | 15770 | https://api.apify.com/v2/key-value-stores/…/records/00002-python-logo.png |
| https://apify.com/robots.txt | stored | robots.txt | text/plain | 203 | https://api.apify.com/v2/key-value-stores/…/records/00003-robots.txt |
| https://apify.com/this-file-does-not-exist-9f3k2.pdf | error | | | | |

*(real rows from a run on Apify on 2026-11-12: 3 files saved = 3 `file` events plus 1 `data-10mb` event for 29,237 bytes; the missing file is an error row and is free)*

**For anyone who has a list of links and needs the files themselves:** archive PDFs, invoices or reports, collect product
images that a scraper found, back up CSV or JSON exports, pull attachments into an AI or RAG pipeline, or hand a colleague one
ZIP instead of 300 links.

**Try it:** press **Start** with the prefilled example (3 small public files). You get 3 rows and 3 saved files in a few
seconds, for well under 1 cent. Clear the URL list and press Start for a **free demo** (3 files and a ZIP; rows carry
`_demo: true`, not charged).

## What you get
- **Every file saved to the run's key-value store**, under a readable key such as `00001-q3-report.pdf` (input position + file
  name, so two files called `image.jpg` never overwrite each other), with the server's content type.
- **One row per URL**: `url`, `status`, `fileName`, `key`, `publicUrl` (direct download link), `contentType`, `bytes`, `sha256`,
  `inZip`, `finalUrl`, `httpStatus`, `redirects`, `error`, `downloadedAt`. Export it as CSV, JSON or Excel, or read it with the API.
- **Optional ZIP** (`Create ZIP`): all saved files in one `files.zip`, with a link in the run's output tab and in the SUMMARY
  record. Duplicate names get `-2`, `-3`. Images, PDFs and archives are stored as they are (no slow recompression); text files are compressed.
- **Safe file names**: the name comes from the server's `Content-Disposition` header or the URL path. Folder parts (`../`) are
  removed, accents are turned into plain letters, anything other than letters, digits and `. _ - ( ) ' !` becomes `_`, reserved
  Windows names are replaced, long names are shortened (the extension is kept), and a missing extension is added from the content type.
- **SHA-256 checksum** of each file, to spot duplicates or check integrity later.
- **Filters**: keep only some extensions (`pdf`, `jpg`…) or content types (`image/*`, `application/pdf`). Handy when some links
  return an HTML error page instead of the file: `allowedContentTypes: ["application/pdf"]` turns those into free `filtered_out` rows.
- **Limits you control**: max size per file, max total size per run, max size of the ZIP.
- **URLs from a dataset**: pick the dataset of a scraper run and name the field (`url`, `imageUrl`, `product.images`…). If the
  field holds a list of URLs, every one of them is downloaded.

## How to use
1. **File URLs**: one direct link per line (up to 5,000 per run; duplicates are removed). Or choose a **Dataset with URLs**
   and set **URL field in dataset**.
2. Optional: **Create ZIP**, **Allowed extensions**, **Allowed content types**, **Max file size**, **Total size limit**,
   **Concurrency**, **Max connections per host**, **Delay per host**, **Timeout**, **Max redirects**, **Retries**, **User-Agent**.
3. Press **Start**. Open the **Output** tab for the table and the ZIP link.

```json
{
  "urls": ["https://example.com/a.pdf", "https://example.com/b.pdf"],
  "allowedContentTypes": ["application/pdf"],
  "maxFileSizeMb": 50,
  "createZip": true
}
```

From another Actor's results (for example a product scraper with an `images` list on each item):

```json
{ "datasetId": "<defaultDatasetId of that run>", "urlField": "images", "allowedContentTypes": ["image/*"] }
```

## Output
```json
{
  "url": "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf",
  "status": "stored",
  "fileName": "dummy.pdf",
  "key": "00001-dummy.pdf",
  "publicUrl": "https://api.apify.com/v2/key-value-stores/<store>/records/00001-dummy.pdf?signature=…",
  "contentType": "application/pdf",
  "bytes": 13264,
  "sha256": "3df79d34abbca99308e79cb94461c1893582604d68329a41fd4bec1885e6adb4",
  "inZip": true,
  "finalUrl": "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf",
  "httpStatus": 200,
  "redirects": 0,
  "error": null,
  "downloadedAt": "2026-11-12T09:36:57+00:00"
}
```
*(real row from a run on Apify on 2026-11-12)*
- `status` — `stored` (downloaded and saved; charged), `error`, `filtered_out` (extension or content type not allowed),
  `too_large` (over **Max file size**), `skipped_total_limit` (the run reached **Total size limit**) or `invalid_input`. Only `stored` is charged.
- `error` — e.g. `http_404`, `http_403`, `timeout`, `dns_failure`, `blocked_private_address`, `too_many_redirects`,
  `redirect_loop`, `invalid_redirect_url`, `file_too_large`, `total_limit_reached`, `extension_not_allowed: html`,
  `content_type_not_allowed: text/html`, `empty_body`; `invalid_url` for input that is not an http(s) URL.
- `inZip` — `true` / `false` (left out because the ZIP size limit was reached; the file itself is still saved), `null` when no ZIP was made.
- **SUMMARY** record: URLs processed, `stored`, `errors`, `filteredOut`, `tooLarge`, `skippedTotalLimit`, `invalidInput`,
  `storedBytes`, `chargedFiles`, `chargedDataUnits`, `zipUrl`, `zipBytes`, `zipFiles`, `zipSkippedFiles`, `stoppedByChargeLimit`.

## Pricing
Pay per event, two parts:

| Event | Price | When |
|---|---|---|
| Saved file (`file`) | $0.0013 per file ($1.30 per 1,000) | each file downloaded and saved |
| Saved 10 MB block (`data-10mb`) | $0.0005 per block | each started 10 MB of saved data in the run |

**How the 10 MB blocks are counted:** all files saved in one run are added up (1 MB = 1,000,000 bytes) and the total is divided
by 10,000,000 and **rounded up**, once for the whole run — not per file. Examples: 300 images totalling 2.4 MB → 1 block;
3 files of 4 MB (12 MB) → 2 blocks; 1,000 PDFs totalling 850 MB → 85 blocks. The ZIP is a copy and is not counted.

| Example run | Files | Data | Total |
|---|---|---|---|
| 1,000 product images, 150 KB each (150 MB) | $1.30 | 15 × $0.0005 = $0.0075 | $1.3075 |
| 100 PDFs, 2 MB each (200 MB) | $0.13 | 20 × $0.0005 = $0.01 | $0.14 |
| 10 videos, 80 MB each (800 MB) | $0.013 | 80 × $0.0005 = $0.04 | $0.053 |

**Not charged:** failed downloads (404, timeouts, blocked addresses, empty responses), files removed by your filters, files over
your size limits, URLs skipped after the total limit, invalid URLs, retries, the ZIP, and the free demo (empty input).
Set a **maximum charge per run** to cap spend: the run stops cleanly when it is reached, and files downloaded after that point are not kept.
Storage of the saved files follows your Apify plan's normal data retention.

## Notes and limits
- **Plain HTTP, no browser, no crawling.** Only the URLs you give are requested. Links that need a login, a cookie, or a page
  that runs JavaScript before the download starts will fail or return an HTML page (use **Allowed content types** to skip those).
- **Polite by default**: at most 2 connections per host and at least 0.5 s between requests to the same host (adjustable,
  never below 0.1 s), up to 5 redirects, timeouts on every request, up to 2 retries for temporary errors (429, 5xx, timeouts).
- **Safe by design**: addresses that resolve to internal networks (localhost, private ranges, link-local such as
  169.254.169.254, IPv6 loopback/unique-local) are never contacted. Redirects are followed one hop at a time and each hop is
  checked the same way — reported as `blocked_private_address`.
- **Size**: up to 500 MB per file and 500 MB per ZIP. Big files are held in memory while they are saved; with the default 1 GB of
  memory, files up to about 100 MB at the default concurrency should fit (estimate, not yet measured on large files). For larger files or ZIPs, raise the run's memory.
- When the total size limit is reached, no further files are requested.
- **Only download files you have the right to copy and store.** The files belong to their owners; this Actor does not check
  licences or copyright. Respect the terms of the sites you download from.

## FAQ
**Am I charged when a link is broken?** No. Only files that were really downloaded and saved are charged. A 404, a timeout or
an HTML error page removed by your content-type filter costs nothing.

**Why is the data charge per run and not per file?** So that many small files cost almost nothing extra: 1,000 thumbnails of
20 KB each add only 2 blocks ($0.001) on top of the per-file price.

**Can I keep the original file names?** The file name is kept as far as it is safe: `Q3 Report (final).pdf` becomes
`Q3_Report_(final).pdf`. Inside the ZIP the same safe names are used; the storage key adds a 5-digit number in front.

**How do I get all files at once?** Turn on **Create ZIP** and download `files.zip` from the Output tab, or read each `publicUrl`.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Image Converter](https://apify.com/ucchiii/image-converter) — convert images between JPG, PNG, WebP and AVIF, with optional resize
- [PDF Extractor](https://apify.com/ucchiii/pdf-extractor) — PDF to text, tables and metadata from public PDF URLs, page by page
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Bulk HTTP Request](https://apify.com/ucchiii/bulk-http-request) — send many HTTP requests (GET/POST/...) from a list and collect status, headers and body
- [Tesseract OCR](https://apify.com/ucchiii/ocr-image-text) — OCR for image URLs and scanned PDFs with built-in Tesseract: text, confidence and line boxes in 8 languages
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
