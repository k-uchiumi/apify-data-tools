# Sitemap URL Extractor: Export All URLs of a Website

**Run it on Apify:** https://apify.com/ucchiii/sitemap-urls · **Price:** $0.25 per 1,000 URLs · free demo with empty input

**For SEO specialists, developers and crawler builders:** you need the full URL list of a website without crawling it. This Actor finds the sitemaps (robots.txt, sitemap indexes, .gz files) and returns each URL with lastmod, changefreq and priority, with include/exclude filters. **$0.25 per 1,000 URLs.**

Get the **complete list of URLs** a website publishes in its sitemaps — for SEO audits, content inventories,
migrations, change monitoring or as input for other crawlers.

- **Automatic discovery** via `robots.txt` (falls back to `/sitemap.xml` and `/sitemap_index.xml`)
- Follows **sitemap indexes** and **gzipped** sitemaps
- Fields: `url`, `lastmod`, `changefreq`, `priority`, number of images, source sitemap
- **Include / exclude** URLs by regular expression; cap URLs per site
- Reads **sitemaps only** (files published for crawlers) — it does not crawl pages
- **$0.25 per 1,000 URLs**

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
`no_sitemap_found`.

## Related tools
- [url-status-checker](https://apify.com/ucchiii/url-status-checker) — Check the HTTP status and redirects of the extracted URLs
- [dataset-dedup-merge](https://apify.com/ucchiii/dataset-dedup-merge) — Merge sitemap datasets from several sites and dedupe
- [serp-dataset-analyzer](https://apify.com/ucchiii/serp-dataset-analyzer) — Compare your URLs with Google ranking data

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
