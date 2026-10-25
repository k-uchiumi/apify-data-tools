# RSS Feed Reader & Aggregator: RSS, Atom to JSON

**Run it on Apify:** https://apify.com/ucchiii/rss-feed-reader · **Price:** $0.30 per 1,000 items · free demo with empty input

**For marketers, researchers and developers who follow many blogs and news sites.** Fetch **many RSS / Atom feeds
at once** and get one tidy list of articles — for news monitoring, newsletters, content collection or as input for
AI pipelines. **$0.30 per 1,000 items**; an empty input runs a free demo.

- Supports **RSS 2.0, Atom 1.0 and RSS 1.0 (RDF)**
- **Auto-discovery**: give a website URL and the feed is found from the page's `<link rel="alternate">` tag
- Dates normalized to **ISO 8601 UTC**; summaries converted to plain text; optional full content
- Filters: **keywords** (title/summary), **published within N days**, max items per feed
- **Deduplication** across feeds by guid / link
- Reads **feeds only** (files published for machine reading) — it does not scrape pages
- Gentle on servers: usually 1 request per feed (2 when a feed has to be discovered from a page); failed requests are retried once, so at most 4 requests per feed plus redirects. Max 3 in parallel, 0.3 s between requests
- Safe by design: hosts that resolve to internal addresses (localhost, private ranges, link-local such as 169.254.169.254) are never contacted, including redirect targets; such feeds are reported as `blocked_private_address` and not charged
- **$0.30 per 1,000 items** (an empty input runs a free demo)

## Use cases
- Competitor and industry news monitor on a schedule
- Weekly newsletter or digest source list
- Feed articles to an LLM or vector store (see keyword and date filters)

## Input
```json
{ "feeds": ["https://blog.apify.com/rss/", "https://www.python.org"], "maxItemsPerFeed": 50,
  "publishedWithinDays": 7, "keywords": ["python", "ai"], "dedupe": true, "includeContent": false }
```

## Output
```json
{ "feedUrl": "https://blog.apify.com/rss/", "feedTitle": "Apify Blog", "title": "Example post",
  "link": "https://blog.apify.com/example/", "guid": "abc123", "published": "2026-10-01T09:30:00Z",
  "updated": null, "summary": "Plain-text summary...", "categories": ["news"],
  "enclosureUrl": null }
```
`content` is added when **Include full content** is on. Author names are not included (no personal data). A per-feed summary (items found, errors, `no_feed_found`) is saved as `SUMMARY`.
Content belongs to each feed's publisher; check their terms before republishing.

<!-- more-tools:start -->
## More tools from the same developer

- [Podcast Episodes from RSS](https://apify.com/ucchiii/podcast-rss-episodes) — find podcasts and list their episodes (audio URL, duration, date) from RSS
- [Hacker News API](https://apify.com/ucchiii/hacker-news-api) — Hacker News stories, comments and 'Who is hiring' posts via the official APIs
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [RAG Chunker](https://apify.com/ucchiii/rag-chunker) — split scraped pages into chunks for RAG and vector stores
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
