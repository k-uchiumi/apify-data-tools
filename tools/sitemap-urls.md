# Sitemap URL Extractor: Export All URLs of a Website

**Run it on Apify:** https://apify.com/ucchiii/sitemap-urls · **Price:** $0.25 per 1,000 URLs · free demo with empty input

**For SEO specialists, site-migration teams and developers who need every URL of a website.** Get the **complete
list of URLs** published in its sitemaps — no crawling. **$0.25 per 1,000 URLs.**

## Use cases
- SEO audit and content inventory of a whole site
- Pre-migration URL list (feed it to the URL Status Checker to verify redirects)
- Change monitoring via `lastmod`
- Seed list for other crawlers or RAG ingestion

- **Automatic discovery** via `robots.txt` (falls back to `/sitemap.xml` and `/sitemap_index.xml`)
- Follows **sitemap indexes** and **gzipped** sitemaps
- Fields: `url`, `lastmod`, `changefreq`, `priority`, number of images, source sitemap
- **Include / exclude** URLs by regular expression; cap URLs per site
- Reads **sitemaps only** (files published for crawlers) — it does not crawl pages
- Safe by design: hosts that resolve to internal addresses (localhost, private ranges, link-local such as 169.254.169.254) are never contacted, including redirect targets; such sites are reported as `blocked_private_address` and not charged

## Input
```json
{ "sites": ["example.com", "https://example.org/sitemap_index.xml"], "includePattern": "/blog/", "maxUrlsPerSite": 50000 }
```

## Output
```json
{ "url": "https://example.com/blog/post", "lastmod": "2026-10-01", "changefreq": "weekly", "priority": 0.8,
  "images": 1, "site": "https://example.com", "sitemap": "https://example.com/post-sitemap.xml" }
```
A per-site summary (sitemap files read, URLs found) is saved as `SUMMARY`. Sites without a sitemap are reported as
`no_sitemap_found` (or `blocked_private_address`).

<!-- more-tools:start -->
## More tools from the same developer

- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Website Screenshot API](https://apify.com/ucchiii/website-screenshot) — screenshots and PDFs of web pages: full page, mobile, element, no cookie banners
- [RAG Chunker](https://apify.com/ucchiii/rag-chunker) — split scraped pages into chunks for RAG and vector stores
- [SERP Analyzer](https://apify.com/ucchiii/serp-dataset-analyzer) — rankings, share of voice and People Also Ask from Google Search scraper datasets
- [RSS Feed Reader & Aggregator](https://apify.com/ucchiii/rss-feed-reader) — many RSS / Atom feeds merged into one clean article list
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
