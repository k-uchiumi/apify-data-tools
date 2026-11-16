# HTML to PDF API: HTML or URL to PDF, A4 / Letter

**Run it on Apify:** https://apify.com/ucchiii/html-to-pdf · **Price:** $6.50 per 1,000 PDFs · free demo with empty input

**HTML or public URL to PDF, A4 / Letter, header and footer**

**For developers, finance and ops teams, and no-code builders who need invoices, reports and web pages as PDF files without running their own headless browser.**
Send an HTML string or a list of public URLs and get one **PDF** per item, stored in your key-value store with a **signed download link** in the dataset.
Choose **A4 or Letter**, margins, a header and footer with page numbers, and whether backgrounds are printed.
**$0.0065 per PDF — and you only pay when the PDF was stored successfully.** Leave the input empty for a free demo.

## Use cases
- **Invoices, quotes and certificates**: render your own HTML template to a print-ready PDF (backgrounds and brand colors kept)
- **Reports and exports**: turn an HTML report into a paginated PDF with "Page 1 of N" and a date in the header
- **Archiving public pages**: save a documentation page, article or policy as PDF for records (only pages you may copy)
- **Automation**: call it from Make, Zapier, n8n or your own code and use the `fileUrl` field of the result

## Features
- Input: one HTML document, a JSON list of HTML documents, public page URLs, or any mix (up to 50 items per run)
- Paper: A4, Letter, Legal, A3, A5; portrait or landscape; or the page's own `@page` size
- Margins per side in millimetres (0 to 50)
- **Header and footer**: plain text with `{page}`, `{pages}`, `{date}`, `{title}`, `{url}` placeholders (or small HTML), plus a one-click page-number footer
- **Background printing** on by default (colors and images), switchable
- Print or screen CSS, scale 0.1 to 2, page ranges such as `1-3`, wait strategy and extra delay for charts
- Optional: hide cookie banners with CSS, block common ad and tracking domains
- Output: the PDF in the key-value store plus a signed link, page count, size and title in the dataset

## Price
| Event | Price |
On the Apify Free plan, the $5 of monthly platform credit covers about **760 PDFs** a month — enough to try it on real data.
|---|---|
| `pdf` (one PDF stored successfully) | $0.0065 |

**Not charged on failures:** timeouts, navigation errors, HTTP 4xx/5xx pages, blocked or unsupported URLs, invalid URLs, HTML that is too large and files that could not be stored. A row is charged only after its PDF is stored. If you set a maximum cost per run, the run stops when the limit is reached. The empty-input demo is free.

## Input
HTML to PDF (invoice on US Letter with page numbers):
```json
{
  "html": "<h1>Invoice #2042</h1><p>Total: $1,280.00</p>",
  "paperFormat": "Letter",
  "headerTemplate": "Example Co. - Invoice #2042",
  "pageNumbers": true,
  "printBackground": true
}
```
Public URLs to PDF:
```json
{
  "urls": ["https://docs.python.org/3/tutorial/index.html"],
  "paperFormat": "A4", "marginTopMm": 20, "marginBottomMm": 20, "pageNumbers": true, "hideCookieBanners": true
}
```
Other fields: `htmlDocuments` (JSON array of HTML strings), `landscape`, `marginRightMm`, `marginLeftMm`, `footerTemplate`, `mediaType` (`print` or `screen`), `scale`, `preferCssPageSize`, `pageRanges`, `waitUntil`, `delayMs`, `blockAds`.
If you set a header or footer, the top or bottom margin is raised to at least 15 mm so the text does not overlap the page. There is no login, cookie, header or form input for URLs.

## Output
One dataset row per item:
```json
{
  "source": "html", "label": "html-1", "url": null, "finalUrl": null, "status": null,
  "title": "Invoice #2042", "paperFormat": "Letter", "pages": 1, "bytes": 20616,
  "fileUrl": "https://api.apify.com/v2/key-value-stores/<storeId>/records/pdf-001-1a2b3c4d5e.pdf?signature=...",
  "key": "pdf-001-1a2b3c4d5e.pdf", "createdAt": "2026-10-25T09:00:00+00:00", "error": null
}
```
The PDF itself is stored in the run's default key-value store as `application/pdf`. `fileUrl` is a signed link that opens without an API token; anyone who has the link can open that file, so share it only where you want to. For URL items `url`, `finalUrl` (after redirects) and `status` are filled in. Failed rows have `fileUrl: null` and `error` set to one of: `invalid_url`, `unsupported_domain`, `blocked_private_address`, `timeout`, `navigation_failed`, `http_<status>`, `http_error`, `html_too_large`, `render_failed`, `pdf_too_large`, `storage_failed`. Demo rows carry `_demo: true`.

## Limits
- 50 items per run (HTML documents and URLs together); extra items are ignored with a warning
- 3 conversions at a time overall, at most 2 at a time per host, 45 s per item; one HTML document up to 2,000,000 characters; one PDF up to 30 MB
- Public pages only: no login, no cookies, no custom headers, no form filling. Pages behind a consent wall that CSS cannot hide, or behind bot protection, may print the wall (or fail)
- Facebook, Instagram, X/Twitter, TikTok, LinkedIn and Threads are refused (`unsupported_domain`)
- Very dynamic pages may need `waitUntil: networkidle` or an extra delay; page content that only appears after a click is not captured

## Safety
Only `http`/`https` URLs are accepted. Every host is resolved first and **localhost, private ranges, link-local (169.254.169.254) and other internal addresses are never contacted** — for the URL itself, each redirect hop and every request a page or your HTML makes (images, fonts, scripts); `file:` addresses are blocked too. Known limit: a DNS answer that changes between the check and the connection (DNS rebinding) is not detected. You are responsible for having the right to convert the pages and content you submit (site terms, copyright, personal data in your own HTML). Files stay in your own storage; nothing is kept by the developer.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Website Screenshot](https://apify.com/ucchiii/website-screenshot) — screenshots and PDFs of web pages: full page, mobile, element, no cookie banners
- [HTML to Markdown Converter for LLM and RAG](https://apify.com/ucchiii/html-to-markdown) — clean Markdown from URLs, raw HTML or datasets for LLM and RAG
- [PDF Extractor](https://apify.com/ucchiii/pdf-extractor) — PDF to text, tables and metadata from public PDF URLs, page by page
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
