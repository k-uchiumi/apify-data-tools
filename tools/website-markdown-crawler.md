# Website to Markdown Crawler for RAG and LLMs

**Run it on Apify:** https://apify.com/ucchiii/website-markdown-crawler · **Price:** $2.00 per 1,000 markdown pages · free demo with empty input

**Website to Markdown Crawler: give it a start URL and it follows the links on that site, turning every page into clean Markdown — one dataset row per page with url, title, description, markdown, word count and depth. Built for RAG pipelines, LLM context and vector databases. Plain HTTP, no browser, robots.txt respected.**

| url | title | wordCount | depth | markdown (start) |
|---|---|---|---|---|
| https://docs.apify.com/academy/scraping-basics-javascript | Web scraping basics for JavaScript devs \| Academy \| Apify Documentation | 997 | 0 | `# Web scraping basics for JavaScript devs` `**Learn how to use JavaScript to extract information from websites in this practical course, starting from the absolute basics.**`… |
| https://docs.apify.com/academy/scraping-with-ai | Web scraping with AI and Apify \| Academy \| Apify Documentation | 426 | 1 | `# Web scraping with AI and Apify` `**Learn how to use AI to extract information from websites, starting from the absolute basics.**`… |

*(real rows from the free demo run on Apify on 2026-11-12; the full row also has description, statusCode, crawledAt, referrerUrl, lang, charCount, truncated and error)*

**For people feeding websites to AI** — documentation for a support bot, a knowledge base for an internal assistant, a blog for a fine-tuning set, product docs for a coding agent — who want the text of a whole site as tidy Markdown without writing a crawler.

**Try it:** press **Start** with the prefilled example (up to 10 pages of a documentation site, about 2 cents). Clear **Start URLs** and press Start for a **free demo** on 5 pages (rows carry `_demo: true`, not charged).

## What you get
- **One row per page**: `url`, `title`, `description`, `markdown`, `wordCount`, `depth`, `statusCode`, `crawledAt`, plus `referrerUrl` (the page that linked to it), `lang`, `charCount`, `truncated` and `error`.
- **Markdown that keeps the structure**: headings (`#`, `##`), numbered and nested lists, tables as GitHub-style pipe tables, code blocks with their language (```` ```python ````), bold and italics, and links made absolute.
- **Only the content**: menus, site headers, footers, sidebars, cookie banners, share buttons, newsletter boxes and ad-like blocks are removed; when a page has a `<main>` or `<article>` element, only that is kept. Turn **Main content only** off to keep everything except scripts and styles.
- **Stays on the site**: by default only pages in the same folder as the start URL (`/docs/…` stays in `/docs/`); or the whole host; or the host and its subdomains. Links to other websites are never followed.
- **You set the limits**: max pages (up to 5,000 per run), max link depth, URL patterns to include or skip, and optionally the site's **sitemaps** to find pages no link points to.
- **RAG-ready chunks (optional)**: turn on **Split into chunks** and every row also gets a `chunks` array — the page split along its headings into pieces of about the size you choose, each with `headingPath` (`["Install the SDK", "Steps"]`), `charCount` and `approxTokens`. Code blocks are never cut in the middle. Chunks cost nothing extra.
- **Any language**: the charset comes from the HTTP header, a byte-order mark or `<meta charset>`, so Japanese, Chinese, Cyrillic and other pages come out right. `wordCount` counts each Chinese, Japanese or Korean character as one word.

## Example: what a page becomes
A documentation page with a site header, a cookie banner, a sidebar, an ad block, share buttons and a footer around its content becomes (the test page in `tests/`, exact output of the converter):

````markdown
# Install the SDK

Use **pip** to install it. See the [client reference](https://docs.example.com/v2/api/client) or
[PyPI](https://pypi.org/project/example/).

## Steps

1. Create a virtual environment
2. Install the package
   - stable
   - beta

```bash
pip install example-sdk
example --version
```

## Options

| Name | Default |
| --- | --- |
| timeout | 30 |
| retries | 3 |
````

*(output of the converter on a sample page from the test suite; nothing from the header, banner, sidebar, ad, share buttons or footer is left)*

## How to use
1. Put one or more pages in **Start URLs** — usually the top of the docs or blog you want (up to 100 start URLs). A sitemap URL such as `https://example.com/sitemap.xml` also works.
2. Choose **Max pages** (default 100) and **Max link depth** (default 5). Optional: **Scope**, **Only URLs matching** / **Skip URLs matching**, **Read sitemaps**, **Split into chunks**.
3. Press **Start**, or call the Actor from your code:

```json
{
  "startUrls": [{ "url": "https://docs.example.com/guide/" }],
  "maxPages": 300,
  "maxDepth": 4,
  "scope": "folder",
  "excludeUrlPatterns": ["**/changelog/**", "**?page=*"],
  "splitIntoChunks": true,
  "chunkSize": 1500,
  "chunkOverlap": 200
}
```

**URL patterns**: `**` matches anything, `*` matches anything except `/`, and a pattern without `*` matches when the URL contains it. `https://example.com/docs/**` keeps only the docs; `**/tag/**` skips tag pages; `/login` skips any URL containing `/login`. Start URLs are always fetched; pages that do not pass the patterns are not fetched, so links on them are not followed.

## Output fields
- `url` — the page that was read, after redirects (no `#fragment`, no `utm_` tracking parameters). Two URLs that end on the same page give one row.
- `title` — the `<title>` tag, else `og:title`, else the first `<h1>`. `description` — the meta description, else `og:description`.
- `markdown` — the page content as Markdown; `wordCount`, `charCount`; `truncated` is true when **Max characters per page** cut it (or the page was over 5 MB).
- `depth` — 0 for start URLs and sitemap pages, 1 for pages they link to, and so on. `referrerUrl` — where the link was found.
- `statusCode`, `crawledAt` (UTC), `lang`, and `chunks` when chunking is on.
- `error` — `null` on success, otherwise `blocked_by_robots`, `robots_txt_unreachable`, `crawl_delay_too_long`, `blocked_private_address`, `invalid_url`, `timeout`, `dns_failure`, `connection_error`, `tls_error`, `http_404` (or another `http_<status>`), `too_many_redirects`, `redirect_loop`, `redirected_out_of_scope`, `not_html` or `no_content` (an HTML page without text, for example one built entirely by JavaScript).
- **SUMMARY** record: pages fetched, pages turned into Markdown, rows charged, pages skipped by robots.txt, errors by code, duplicates, and whether the run stopped at the page limit or the maximum charge.

To load the result into a vector database, read the dataset items and embed either `markdown` (one document per page) or each entry of `chunks` (with `url` and `headingPath` as metadata).

## Pricing
Pay per event: **$0.002 per Markdown page** — a crawled page turned into Markdown. That is **$2.00 per 1,000 pages** on the free plan; with Store discounts it is $1.80 (BRONZE), $1.60 (SILVER) and **$1.40 per 1,000 (GOLD)**.

**Not charged:** pages skipped because of robots.txt, fetch errors (404, 5xx, timeouts, blocked addresses), redirects off the site, non-HTML files (PDFs, images, downloads), pages without text content, and the free demo (empty input). Chunks are included in the page price.

On the Apify Free plan, the $5 of monthly credit covers about 2,500 pages a month. Set a **maximum charge per run** to cap spend: the crawler stops fetching new pages when it is reached.

## Polite crawling
- **robots.txt is always followed** — read once per host, with the rules for `website-markdown-crawler` if the site names it, otherwise the rules for `*` (longest matching rule wins, `*` and `$` supported). If robots.txt cannot be reached (server error), the host is not crawled. A `Crawl-delay` is honored; hosts asking for more than 60 seconds between requests are skipped.
- `<meta name="robots" content="nofollow">` and `rel="nofollow"` links are not followed.
- At most **2 connections per host** and at least **0.5 s between requests** to the same host, whatever the concurrency. One retry after 429/502/503/504, a timeout or a dropped connection.
- At most **5 MB of HTML** read per page and **5,000 pages** per run. Links to images, PDFs, archives, media and other files are not fetched at all; a response that turns out not to be HTML is not read.
- No login, no cookies, no forms, no JavaScript. The User-Agent names this Actor.
- **Safe by design**: addresses inside private networks (localhost, 10.x, 192.168.x, link-local such as 169.254.169.254, IPv6 loopback/unique-local) are never contacted — every redirect hop is checked before it is followed.

Only crawl sites you are allowed to access, and check each site's terms before reusing its content.

## Limits
- HTTP only: pages that build their text with JavaScript after loading come back empty (`no_content`, not charged).
- Up to 5,000 pages and 100 start URLs per run. For bigger sites, split the work by folder (one run per section) with **Only URLs matching**.
- Pages are processed in parallel, so the dataset order is roughly breadth-first, not strictly the order of links.

## FAQ
**Why did it stop after a few pages?** Check **Scope** (the default stays in the start URL's folder), **Max link depth**, your URL patterns, and the SUMMARY record (`robotsBlocked`, `byError`). Turning on **Read sitemaps** often finds pages that menus built by JavaScript hide.

**Do I pay for pages blocked by robots.txt or broken links?** No. Only rows with Markdown are charged.

**Can I get only the text for embeddings?** Yes: keep **Main content only** on and **Keep images** off (both defaults), and turn on **Split into chunks** if your pipeline wants pieces rather than whole pages.

**Which libraries does it use?** beautifulsoup4 (MIT), lxml (BSD-3-Clause), markdownify (MIT), httpx (BSD-3-Clause) and the Apify SDK (Apache-2.0).

<!-- more-tools:start -->
## More tools from the same developer

- [HTML to Markdown Converter for LLM and RAG](https://apify.com/ucchiii/html-to-markdown) — clean Markdown from URLs, raw HTML or datasets for LLM and RAG
- [RAG Chunker](https://apify.com/ucchiii/rag-chunker) — split scraped pages into chunks for RAG and vector stores
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [Broken Link Checker](https://apify.com/ucchiii/broken-link-checker) — find broken links on web pages: status of every link found
- [Link Preview API](https://apify.com/ucchiii/link-preview-api) — link previews for URL lists: Open Graph and Twitter Card title, description, image, site name and favicon
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
