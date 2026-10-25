# PDF Extractor: PDF to Text, Tables & Metadata

**Run it on Apify:** https://apify.com/ucchiii/pdf-extractor · **Price:** $4.00 per 1,000 pdf extracteds · free demo with empty input

**For developers, analysts and AI/RAG pipelines that need the contents of PDFs as clean data.** Give it public PDF URLs and
get **per-page text**, **tables as rows and cells** and document metadata (title, author, producer, page count). A
**pdf to text** and **pdf table extractor** in one Actor, with no OCR and no files to upload. **$0.004 per PDF (first 20 extracted pages included) + $0.0005 per extra page.**
Leave the input empty for a free demo.

## Use cases
- Feed reports, filings, manuals and papers into a RAG pipeline or LLM, page by page with page numbers
- Pull tables out of financial reports, price lists and government statistics into JSON/CSV
- Build a searchable text index of a PDF library, with title and author from the metadata
- Spot scanned documents (image-only pages) before sending them to an OCR service

## How it works
- Each PDF is downloaded once, then parsed with pdfplumber (MIT licence). Text and tables come from the PDF's own text layer.
- `pages` limits the range (`1-5,8`, `3-`), `maxPagesPerPdf` caps long documents (default 200, max 2000).
- Two output shapes: `perPage` (one row per page) or `perDocument` (one row per PDF with all text and tables).
- **Scanned pages**: a page with no text layer and only images is returned with `scanned: true` and empty text. There is **no OCR**.
- **Pay only for results**: a page is charged only when text or tables were extracted. Failed downloads, unreadable PDFs, blank pages and scanned pages are free. The free demo (empty input) is not charged.
- Safe by design: URLs that resolve to internal addresses (localhost, private ranges, link-local such as 169.254.169.254) are never contacted, including redirect targets (`blocked_private_address`). Max 5 redirects, 50 MB per file, 60 s per download, `%PDF` signature required.
- Per-document `SUMMARY` in the key-value store: title, author, producer, pageCount, pagesExtracted, pagesCharged, scannedPages, error.

## Input
```json
{ "urls": ["https://example.com/report.pdf"], "pages": "1-5,8", "extractTables": true, "outputMode": "perPage", "maxPagesPerPdf": 200 }
```

## Output (perPage)
```json
{
  "url": "https://example.com/report.pdf", "page": 1, "text": "Quarterly Report ...", "charCount": 1834, "scanned": false, "error": null,
  "tables": [{"header": ["Region", "Revenue", "Growth"], "rows": [["North", "1,200", "5%"], ["South", "900", "7%"]]}]
}
```
`header` is the first row of a table when it looks like a header (all cells filled, no numbers), otherwise `null` and the first row stays in `rows`.
`error` is e.g. `http_404`, `not_a_pdf`, `file_too_large`, `timeout`, `too_many_redirects`, `encrypted`, `unreadable_pdf`, `blocked_private_address`, `bad_pages:...`
(a failed PDF produces one row with `page: 0`). In `perDocument` mode each row has `text`, `tables` (each with its `page`), `title`, `author`, `producer`, `pageCount`, `pagesExtracted`, `scannedPages`, `error`.

## Pricing
$0.004 per PDF (pay per event, `pdf`; includes the first 20 extracted pages) + $0.0005 per extra page (`page`). Example: a 10-page PDF costs $0.004, a 100-page PDF $0.044. Failed, scanned-only and empty PDFs are free. For comparison, other PDF text Actors in the Apify Store charge about $0.003 to $0.02 per PDF (per document) or $0.003 to $0.005 per page.

## Limits and notes
- Text order and table detection follow the PDF's layout; complex multi-column pages and borderless tables may need checking.
- Password-protected PDFs are not opened (`encrypted`). Only the PDFs you list are downloaded; links inside them are not followed.
- Only process PDFs you are allowed to download. The text belongs to the document's rights holder.

<!-- more-tools:start -->
## More tools from the same developer

- [OCR Image to Text](https://apify.com/ucchiii/ocr-image-text) — OCR for image URLs and scanned PDFs with built-in Tesseract: text, confidence and line boxes in 8 languages
- [RAG Chunker](https://apify.com/ucchiii/rag-chunker) — split scraped pages into chunks for RAG and vector stores
- [Website Screenshot API](https://apify.com/ucchiii/website-screenshot) — screenshots and PDFs of web pages: full page, mobile, element, no cookie banners
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
