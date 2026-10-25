# HTML to Markdown Converter for LLM and RAG

**Run it on Apify:** https://apify.com/ucchiii/html-to-markdown · **Price:** $1.00 per 1,000 page converteds · free demo with empty input

Turn web pages, raw HTML or a dataset of HTML into **clean Markdown**, one row per document. Built for people who feed
documents to an LLM, a RAG pipeline or a docs site and are tired of navigation menus, cookie banners, footers and
script junk in their text. No browser, no LLM, no API key.

**Try it free: leave input empty and press Start.** The demo converts two sample pages and is not charged (rows carry `_demo: true`).

| source | title | markdown (shortened) | wordCount | status |
|---|---|---|---|---|
| https://example.com/blog/hello-markdown | Hello Markdown (sample) | `# Hello Markdown` ... `\| Plan \| Price \|` | 24 | ok |
| https://example.com/docs/api | API reference (sample) | `# API reference` ... a `json` code block | 17 | ok |
| https://example.com/missing | | | 0 | error (`http_404`) |

## What you get
- **Main-content extraction**: nav, header, footer, aside, scripts, styles, forms and hidden elements are removed; if the page has `<main>` or `<article>`, only that is kept
- **Tables** become GitHub-flavored Markdown tables (or plain lines if you turn them off)
- **Code blocks keep their language** (` ```python `), read from `class="language-..."`, `lang-...`, `highlight-source-...` or `data-lang`
- **Absolute links and image URLs**, resolved against the page URL (`javascript:` links are removed)
- **Images**: dropped (default) or kept as `![alt](src)`
- **Front matter** with `title` and `url` (YAML) if you want it
- **Max characters** per document, cut at a paragraph boundary, with a `truncated` flag
- Three inputs, mixable in one run: URLs, raw HTML strings, or an Apify dataset (any field name)
- Works for English and Japanese; `wordCount` counts words, or characters for Japanese, Chinese and Korean text

## Price
**$1.00 per 1,000 documents** ($0.001 per document converted). 1,000 pages cost $1, 10,000 pages cost $10.
Only successful conversions are charged. Failed fetches (404, timeout, blocked, too large), empty pages and the demo are free.

## Input
```json
{
  "urls": ["https://example.com/docs/intro"],
  "htmlStrings": ["<main><h1>Hello</h1></main>"],
  "baseUrl": "https://example.com",
  "datasetId": "<dataset ID of a run that stored HTML>",
  "htmlField": "html",
  "mainContent": true,
  "keepTables": true,
  "absoluteLinks": true,
  "imageMode": "drop",
  "frontMatter": false,
  "maxChars": 0
}
```
Up to 10,000 inputs per run. Leave URLs, HTML and dataset empty for the free demo.

## Output (one row per input)
```json
{
  "source": "https://example.com/blog/hello-markdown",
  "url": "https://example.com/blog/hello-markdown",
  "title": "Hello Markdown (sample)",
  "markdown": "# Hello Markdown\n\nTurn **web pages** into clean Markdown ...",
  "wordCount": 24, "charCount": 312, "truncated": false,
  "status": "ok", "error": null
}
```
`status` is `ok`, `empty` (the page had no text) or `error`. `error` holds a short code such as `http_404`, `timeout`,
`dns_failure`, `page_too_large`, `blocked_private_address`, `unsupported_content_type: ...`, `invalid_url`, `empty_input`
or `missing_html_field: html`. If a page redirected, `finalUrl` is added. For datasets, `source` is the item's `url`
(or `dataset:<id>#<index>`). Counts of ok / empty / error are saved in the key-value store as `STATS`.

## Fetching rules
- Only `http://` and `https://` URLs. Redirects are followed (up to 10) and **every hop is checked**; localhost, private,
  link-local (cloud metadata) and other internal addresses are refused (`blocked_private_address`)
- Pages larger than **5 MB** are skipped (`page_too_large`); only HTML and text content types are converted
- Polite: at most 2 connections per host and at least 0.2 s between requests to the same host
- The Actor does not crawl, does not run JavaScript and does not log in. Pages that need JavaScript to show their content
  return what the server sent; for those, fetch the rendered HTML with a browser-based Actor and pass it in as a dataset

## Use cases
- Prepare documentation for an LLM or a vector database (pair it with a chunker)
- Archive articles as Markdown with front matter for a static site or knowledge base
- Clean HTML exported from a CMS or a scraper into readable text
- Cut every page to a fixed length for prompt budgets

## Notes
You are responsible for having the right to fetch and reuse the pages you convert, and for following each site's terms and
robots.txt. This Actor fetches only the URLs you list and adds no personal data.

<!-- more-tools:start -->
## More tools from the same developer

- [RAG Chunker](https://apify.com/ucchiii/rag-chunker) — split scraped pages into chunks for RAG and vector stores
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [PDF Extractor](https://apify.com/ucchiii/pdf-extractor) — PDF to text, tables and metadata from public PDF URLs, page by page
- [Bulk HTTP Request](https://apify.com/ucchiii/bulk-http-request) — send many HTTP requests (GET/POST/...) from a list and collect status, headers and body
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
