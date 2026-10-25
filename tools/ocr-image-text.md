# OCR Image to Text: Extract Text from Images & PDFs

**Run it on Apify:** https://apify.com/ucchiii/ocr-image-text · **Price:** $4.00 per 1,000 image reads · free demo with empty input

**For developers, analysts and AI/RAG pipelines that need the text inside images.** Give it public image URLs (PNG, JPEG, WebP, TIFF, GIF)
and optionally scanned PDF URLs, and get **one row per image** with the recognized text, language, confidence, word count and image size.
Runs the open-source **Tesseract OCR** engine inside the Actor: **no external OCR API, no API key, nothing to upload.**
**$0.004 per image read.** Leave the input empty for a free demo.

## Use cases
- Turn screenshots, photos of signs, receipts, invoices and slides into searchable text
- Read scanned PDFs (each page is rasterized and read as an image) before indexing them for search or RAG
- Add alt-text or keyword data to image libraries and product catalogs
- Get line-level bounding boxes to locate text in an image (`lines` layout)
- Pre-process a batch of image links from a scraper before sending the text to an LLM

## How it works
- Each URL is downloaded once. Images are converted to grayscale with contrast stretching and small images are upscaled (both can be turned off), then read with Tesseract.
- **Languages**: English, German, French, Spanish, Portuguese, Italian, Japanese and Simplified Chinese. Combine up to 4 (for example English + German). Fewer languages are faster and more accurate.
- **Scanned PDFs**: pages are rendered at 200 dpi and read one by one, up to `maxPdfPages` (default 5, max 50). Each page is one row with its `page` number and counts as one image for pricing.
- **Output layout**: `text` (plain text, paragraphs separated by a blank line) or `lines` (the same plus each line's text, bounding box and confidence).
- **Pay only for results**: an image or PDF page is charged only when at least one word was recognized. Failed downloads, unreadable files, password-protected PDFs and images without text are free. The free demo (empty input, two built-in sample images) is not charged.
- Safe by design: URLs that resolve to internal addresses (localhost, private ranges, link-local such as 169.254.169.254) are never contacted, including redirect targets (`blocked_private_address`). Only `http` and `https`, max 5 redirects, 30 s per download, size cap (default 10 MB, max 25 MB), images above 40 megapixels are skipped.

## Input
```json
{
  "urls": ["https://example.com/receipt.jpg", "https://example.com/scan.pdf"],
  "languages": ["eng", "deu"],
  "outputLayout": "text",
  "maxImageSizeMB": 10,
  "maxPdfPages": 5,
  "grayscale": true,
  "upscaleSmall": true
}
```

## Output
```json
{
  "url": "https://example.com/receipt.jpg",
  "page": null,
  "text": "Invoice No. 2026-0042\nTotal due: USD 1,234.50",
  "language": "eng+deu",
  "confidence": 93.4,
  "wordCount": 8,
  "width": 1000,
  "height": 220,
  "error": null
}
```
`confidence` is the mean word confidence from 0 to 100. `page` is `null` for images and the 1-based page number for PDFs.
With `"outputLayout": "lines"` each row also has `lines`: `[{"text": "Invoice No. 2026-0042", "left": 40, "top": 40, "width": 520, "height": 44, "confidence": 95.1}]`
(pixels of the original image).
`error` is e.g. `http_404`, `not_an_image`, `file_too_large`, `timeout`, `too_many_redirects`, `unreadable_image`, `image_dimensions_too_large`,
`unreadable_pdf`, `encrypted`, `ocr_timeout`, `blocked_private_address`, `invalid_url`. A failed file produces one row with empty text.
A `SUMMARY` record in the key-value store has the counts (rows, ok, errors, imagesCharged, words).

## Pricing
$0.004 per image read (pay per event, `image`). A scanned PDF costs $0.004 per page read. Example: 100 images cost $0.40, a 5-page scanned PDF $0.02.
Images without any recognized text and failed files are free. For comparison, other OCR Actors in the Apify Store charge roughly $0.003 to $0.005 per image or page.

## Limits and notes
- **Printed text works best.** Clean, horizontal, high-contrast text (documents, screenshots, signs, receipts) is read well. **Handwriting is not supported**, and stylized fonts, curved text, very low resolution and heavy noise give poor results. Check `confidence`.
- Tables and multi-column layouts are returned as lines in reading order, not as a structured table.
- Japanese and Chinese text is slower than Latin scripts. Pick only the languages you need.
- Only the files you list are downloaded; links inside them are not followed. Files behind a login return `http_401` or `http_403`.
- Only process images you are allowed to download. The text belongs to the rights holder of the image.

<!-- more-tools:start -->
## More tools from the same developer

- [PDF Extractor](https://apify.com/ucchiii/pdf-extractor) — PDF to text, tables and metadata from public PDF URLs, page by page
- [RAG Chunker](https://apify.com/ucchiii/rag-chunker) — split scraped pages into chunks for RAG and vector stores
- [Website Screenshot API](https://apify.com/ucchiii/website-screenshot) — screenshots and PDFs of web pages: full page, mobile, element, no cookie banners
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
