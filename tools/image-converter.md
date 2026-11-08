# Image Converter: JPG, PNG, WebP, AVIF with Resize

**Run it on Apify:** https://apify.com/ucchiii/image-converter · **Price:** $1.00 per 1,000 converted images · free demo with empty input

**Image converter for image URLs: JPG, PNG, WebP, GIF, BMP and TIFF to JPG, PNG, WebP or AVIF, with optional resize**

Paste image links, pick the output format, optionally set a maximum width or height, and press Start. Every image is converted, saved in the run's storage with a download link, and listed as one row with the formats, file size before and after, and the new width and height. One flat price per image, whatever its size.

## What you get

| sourceUrl | status | outputFormat | inputBytes | outputBytes | sizeChangePercent | width | height | downloadUrl |
|---|---|---|---|---|---|---|---|---|
| https://example.com/photo.jpg | ok | webp | 2,481,733 | 412,904 | -83.4 | 1600 | 1067 | https://api.apify.com/v2/key-value-stores/.../records/0001-photo.webp |
| https://example.com/logo.png | ok | webp | 88,120 | 21,577 | -75.5 | 800 | 400 | https://api.apify.com/v2/key-value-stores/.../records/0002-logo.webp |
| https://example.com/missing.png | failed | | | | | | | *(error: The server answered HTTP 404 ...)* |

**Try it free:** leave the image list empty and press **Start** — 2 built-in sample images (a PNG with transparency and a JPG photo) are converted to WebP. Not charged; rows carry `_demo: true`.

## How to use
1. **Image URLs**: public links to JPG, PNG, WebP, GIF, BMP or TIFF images, one per line.
2. **Convert to**: WebP (default), JPG, PNG or AVIF.
3. Optional: **Quality** (1–100, default 85, used by JPG, WebP and AVIF), **Max width** / **Max height** in pixels, **Background color for JPG** (replaces transparency, default white).

```json
{
  "imageUrls": ["https://example.com/photo.jpg", "https://example.com/logo.png"],
  "outputFormat": "webp",
  "quality": 80,
  "maxWidth": 1200
}
```

## How the conversion works
- **Resize keeps the aspect ratio** and only shrinks: with max width 1200 a 4000×3000 photo becomes 1200×900, while a 800×600 image stays 800×600. Set both to fit a box.
- **Photo orientation** from the camera (EXIF) is applied, so portrait photos stay upright.
- **Metadata is removed**: EXIF data such as camera details and GPS location is not copied into the output file.
- **Animated GIFs and multi-page TIFFs**: the first frame or page is converted (`animated: true` in the row).
- **Transparency** is kept in PNG, WebP and AVIF; JPG has none, so transparent areas get the background color.

## Output fields
- `sourceUrl`, `status` (`ok` or `failed`), `error` (the reason when it failed)
- `fileName`, `downloadUrl` — the converted file in the run's key-value store
- `inputFormat`, `outputFormat`, `inputBytes`, `outputBytes`, `sizeChangePercent` (negative = smaller)
- `originalWidth`, `originalHeight`, `width`, `height`, `animated`

A **STATS** record sums up images converted, failed and charged and the options used. Download the rows as CSV, Excel or JSON from the run page.

## Limits (cannot be raised by input)
- 1,000 image URLs per run; 25 MB per input file; 50 megapixels per image (protection against "decompression bomb" files)
- WebP allows at most 16,383 pixels per side — set a max width or height for bigger images
- Public images only (no login); links to internal network addresses are blocked
- Set a maximum charge per run to cap spend: the run stops cleanly when it is reached and keeps the images converted so far

## Pricing
Pay per event: **$0.001 per converted image**, the same for a 20 KB icon and a 25 MB photo. Failed downloads, unreadable or oversized images and the demo are free.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 5,000 images a month.

## FAQ
**Is the quality loss visible?** At quality 80–90, JPG and WebP look the same as the original to most people while being much smaller. Use PNG for lossless output.

**Which format should I pick?** WebP for websites (small, supported by all current browsers), JPG for maximum compatibility, PNG for lossless graphics and screenshots, AVIF for the smallest files where it is supported.

**Can I convert HEIC or SVG?** Not at the moment. Supported inputs are JPG, PNG, WebP, GIF, BMP and TIFF.

**How long are the files kept?** As long as the run's storage is kept under your Apify plan's data retention. Download them or copy them to your own storage if you need them longer.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Tesseract OCR](https://apify.com/ucchiii/ocr-image-text) — OCR for image URLs and scanned PDFs with built-in Tesseract: text, confidence and line boxes in 8 languages
- [Website Screenshot](https://apify.com/ucchiii/website-screenshot) — screenshots and PDFs of web pages: full page, mobile, element, no cookie banners
- [Free Stock Photo Search](https://apify.com/ucchiii/stock-photos-openverse-search) — openly licensed images with license and ready-to-paste attribution
- [URL to PDF](https://apify.com/ucchiii/url-to-pdf) — save public web pages as PDF files: A4 or Letter, margins, page numbers
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
