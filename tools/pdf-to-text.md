# PDF to Text Converter: Plain Text per Page, No Start Fee

**Run it on Apify:** https://apify.com/ucchiii/pdf-to-text · **Price:** $0.35 per 1,000 extracted pages · free demo with empty input

**PDF to text, page by page: paste public PDF links and get the plain text of every page, plus one full-text .txt file per PDF.**

Each page becomes one row with the page number, the text and its character count, so you can search, filter or feed the text to an LLM page by page. Next to the rows, every PDF gets a `.txt` file with all its pages and a download link. No tables, no OCR, no layout guessing — just the text layer of the PDF, fast and cheap.

**Try it free:** leave the input empty and press **Start** — a small public PDF is converted (not charged, rows carry `_demo: true`).

## What you get

| url | page | text | charCount | scanned | textFileUrl |
|---|---|---|---|---|---|
| https://example.com/annual-report.pdf | 1 | Annual Report 2025\nRevenue grew 12% ... | 2,814 | false | https://api.apify.com/v2/key-value-stores/…/records/001-annual-report.txt |
| https://example.com/annual-report.pdf | 2 | Letter from the CEO\nDear shareholders ... | 3,102 | false | (same file) |
| https://example.com/annual-report.pdf | 3 | | 0 | true | (same file) |
| https://example.com/missing.pdf | 0 | | 0 | false | `error: http_404` |

*(illustrative rows showing the output format; the free demo shows real results)*

- **One row per page**: `url`, `page` (1-based), `text`, `charCount`, `scanned`, `textFileUrl`, `error`.
- **One .txt file per PDF** in the run's key-value store (`001-annual-report.txt`, `002-…`): all extracted pages in order, separated by a form-feed character (`\f`, the same page break `pdftotext` uses). The link is in every row of that PDF and in the `STATS` record.
- **Scanned pages are free**: a page that is only an image has no text layer; it comes back with empty `text`, `scanned: true` and is not charged. This Actor does not run OCR.

## How to use
1. **PDF URLs**: public links to PDF files, one per line (up to 500 per run).
2. Optional **Pages**: a range such as `1-5,8`, `3-` (page 3 to the end) or `-2` (the first two pages), applied to every PDF.
3. Optional **Max pages per PDF** (default 200, up to 2,000).

```json
{
  "urls": [
    "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf",
    "https://www.irs.gov/pub/irs-pdf/fw9.pdf"
  ],
  "pages": "1-2",
  "maxPagesPerPdf": 200
}
```

## Output fields
- `url` — the PDF link you gave.
- `page` — page number (1-based); `0` on the single row of a PDF that could not be read.
- `text` — the page's text, with line breaks kept and surrounding white space trimmed.
- `charCount` — number of characters in `text`.
- `scanned` — `true` when the page has images but no text (a scan); `false` for text pages and blank pages.
- `textFileUrl` — download link of the PDF's full-text `.txt` file (empty when no page had text).
- `error` — `null`, or why the PDF or page failed: `http_404`, `not_a_pdf`, `file_too_large`, `timeout`, `encrypted`, `unreadable_pdf`, `blocked_private_address`, `invalid_url`, `page_failed:…`.
- **STATS** record: per PDF the page count, pages extracted, pages with text, pages charged, scanned pages, the `.txt` file and any error.

## Limits (cannot be raised by input)
- Up to **500 PDFs per run**, each up to **50 MB**, downloaded once within **60 seconds**, following at most 5 redirects.
- Up to **2,000 pages per PDF** (`maxPagesPerPdf`, default 200).
- Public files only: no login, no cookies. Password-protected PDFs return `encrypted`. Links to internal network addresses are blocked.
- Set a **maximum charge per run** to cap spend: the Actor checks it before it starts, stops cleanly when it is reached and keeps the pages read so far.
- Very long jobs: dense pages take a fraction of a second each; for thousands of pages per run, raise the run timeout (default 1 hour).

## Pricing
Pay per event: **$0.00035 per extracted page** — a page that contains text. That is **$0.35 per 1,000 pages**; a 10-page PDF costs $0.0035. No start fee and no per-file fee.
Blank pages, scanned (image-only) pages, PDFs that fail to download or open, and the demo are **free**.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 14,000 pages a month.

## FAQ
**Does it read scanned PDFs?** No. Scans have no text layer and need OCR, which this Actor does not do. Those pages are flagged `scanned: true` and cost nothing, so you can see which documents need OCR.

**Does it extract tables?** No — table cells come out as plain text lines in reading order. If you need tables as rows and cells, use the PDF Extractor Actor from the same developer.

**Why is the text order sometimes odd?** The text is read from the PDF's own text layer. Multi-column layouts and text in floating boxes are put in reading order line by line, which can mix columns on some documents.

**Can I give a local file?** Upload it somewhere public first (for example an Apify key-value store or any file host) and paste the link.

**Who owns the text?** The rights stay with the owners of the documents. Only process PDFs you are allowed to use.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [PDF Extractor](https://apify.com/ucchiii/pdf-extractor) — PDF to text, tables and metadata from public PDF URLs, page by page
- [Tesseract OCR](https://apify.com/ucchiii/ocr-image-text) — OCR for image URLs and scanned PDFs with built-in Tesseract: text, confidence and line boxes in 8 languages
- [URL to PDF](https://apify.com/ucchiii/url-to-pdf) — save public web pages as PDF files: A4 or Letter, margins, page numbers
- [RAG Chunker](https://apify.com/ucchiii/rag-chunker) — split scraped pages into chunks for RAG and vector stores
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
