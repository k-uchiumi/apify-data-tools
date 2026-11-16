# URL to PDF: Save Web Pages as PDF, A4 or Letter

**Run it on Apify:** https://apify.com/ucchiii/url-to-pdf · **Price:** $6.50 per 1,000 rendered PDFs · free demo with empty input

**URL to PDF: save public web pages as PDF files, rendered by a real Chrome browser.**

Paste page URLs, pick the paper size, press **Start**: each page is opened in headless Chromium and printed to a PDF, like *Save as PDF* in your browser — but for up to 50 pages at once, with the same settings every time. Every PDF is stored in the run's key-value store and gets a download link in the results table.

**Try it free:** leave the URL list empty and press **Start** — a built-in sample page is saved as an A4 PDF with page numbers (not charged, the row carries `_demo: true`).

## What you get

| url | status | title | paperFormat | landscape | pages | bytes | fileUrl |
|---|---|---|---|---|---|---|---|
| https://www.python.org/about/ | 200 | About Python | A4 | false | 3 | 412,330 | https://api.apify.com/v2/key-value-stores/…/records/pdf-001-www.python.org-….pdf |
| https://example.com/report | 200 | Q3 report | Letter | true | 7 | 1,035,104 | https://api.apify.com/v2/key-value-stores/…/records/pdf-002-example.com-….pdf |

- **Paper and layout:** A4, US Letter, US Legal, A3 or A5; portrait or landscape; margins in millimetres; scale 0.1 to 2.
- **Looks like the page:** background colors and images are printed by default; choose the page's print stylesheet (default, like a browser's Print dialog) or its screen look.
- **Clean pages:** common cookie banners are hidden with CSS (nothing is clicked or accepted); optionally block common ad and tracking domains.
- **Late content:** wait for `load`, `domcontentloaded` or `networkidle`, plus an extra delay of up to 10 seconds for charts and animations.
- **Page numbers:** optional "Page 1 of N" footer.

## How to use
1. **Page URLs**: public `http(s)` pages, one per line (up to 50).
2. Choose **Paper size**, **Landscape**, margins, **Print backgrounds** and other options.
3. Press **Start** and open the PDFs from the `fileUrl` column, or download them from the run's key-value store.

```json
{
  "urls": ["https://www.python.org/about/", "https://example.com/pricing"],
  "paperFormat": "Letter",
  "landscape": false,
  "marginTopMm": 10,
  "marginBottomMm": 15,
  "printBackground": true,
  "pageNumbers": true
}
```

## Output fields
- `url`, `finalUrl` (after redirects), `status` (HTTP status of the page), `title`
- `paperFormat`, `landscape`, `pages`, `bytes`
- `fileUrl` — signed download link to the PDF, `key` — its name in the key-value store
- `error` — `invalid_url`, `unsupported_domain`, `blocked_private_address`, `timeout`, `navigation_failed`, `http_404` and other `http_4xx/5xx`, `render_failed`, `pdf_too_large`, `storage_failed`; `null` on success
- **STATS** record: URLs, PDFs saved, failures, PDFs charged and whether the run stopped at the maximum charge

## Limits (cannot be raised by input)
- Up to 50 URLs per run; 45 seconds to load each page and 60 seconds to print it; PDFs up to 30 MB each
- At most 3 pages at a time and 2 at a time from the same site; links are not followed (no crawling)
- No login, cookies or custom headers: only pages anyone can open. Social networks (Facebook, Instagram, X/Twitter, TikTok, LinkedIn, Threads) are refused
- Internal network addresses (localhost, private ranges, 169.254.169.254) are blocked, including redirects and every image, script or font the page loads
- Want to turn your own HTML into PDF instead of a URL? Use the HTML to PDF API from the same developer.

## Pricing
Pay per event: **$0.0065 per rendered PDF**, no start fee. Only PDFs that were saved are charged: failed, timed-out, blocked and unsupported URLs and the demo are free. Set a maximum charge per run and the Actor stops rendering before it is exceeded.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 760 PDFs a month.

## FAQ
**Why does my PDF look different from the screen?** By default the page's print stylesheet is used, like Chrome's Print dialog — many sites hide menus and ads for print. Set **CSS media type** to `screen` to keep the on-screen look.

**The page is cut off or too wide.** Try **Landscape**, or a **Scale** of 0.8.

**Charts or images are missing.** Use **Wait until** `networkidle` and an **Extra delay** of 1,000–3,000 ms.

**Is the PDF text searchable?** Yes. Chromium prints real text, so you can search and copy it.

**Who may I convert?** Only pages you are allowed to copy. You are responsible for the site's terms and copyright.

<!-- more-tools:start -->
## More tools from the same developer

- [HTML to PDF API](https://apify.com/ucchiii/html-to-pdf) — HTML strings or public URLs to PDF: A4 / Letter, margins, header, footer, page numbers
- [Website Screenshot](https://apify.com/ucchiii/website-screenshot) — screenshots and PDFs of web pages: full page, mobile, element, no cookie banners
- [PDF to Text Converter](https://apify.com/ucchiii/pdf-to-text) — plain text per page from public PDF URLs, plus one .txt file per PDF
- [HTML to Markdown Converter for LLM and RAG](https://apify.com/ucchiii/html-to-markdown) — clean Markdown from URLs, raw HTML or datasets for LLM and RAG
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
