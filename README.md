# Data tools for the Apify platform

Small, reliable Apify Actors for data extraction and processing — built on official public APIs and on
processing your own Apify datasets (no logins, no personal data). Pay per result; every tool has a free demo
when run with empty input.

| Tool | What it does | Price |
|---|---|---|
| [Career Site Jobs Scraper: Greenhouse, Lever, Ashby API](tools/career-site-jobs.md) · [Run](https://apify.com/ucchiii/career-site-jobs) | Scrape open jobs from company career pages via public Greenhouse, Lever, Ashby, Workable APIs. Unified fields, salary, CSV/JSON export. $1/1k jobs. | $1.00 per 1,000 jobs |
| [Google Maps Leads Cleaner: Dedupe, Phones, CSV-ready](tools/maps-leads-cleaner.md) · [Run](https://apify.com/ucchiii/maps-leads-cleaner) | Clean Google Maps scraper results into leads: dedupe places, E.164 phones, website domains, rating and category filters, CSV export. $0.50/1k. | $0.50 per 1,000 leads |
| [Sitemap URL Extractor: Export All URLs of a Website](tools/sitemap-urls.md) · [Run](https://apify.com/ucchiii/sitemap-urls) | Extract every URL from a website's sitemaps: robots.txt discovery, sitemap indexes, .gz files, lastmod, priority, regex filters, CSV export. $0.25/1k URLs. | $0.25 per 1,000 URLs |
| [Japanese Text Toolkit: Furigana, Romaji, Wareki Converter](tools/japanese-text-toolkit.md) · [Run](https://apify.com/ucchiii/japanese-text-toolkit) | Bulk convert Japanese text: furigana (kana readings), Hepburn romaji, wareki to western dates, kanji numerals, full/half-width. API and CSV ready. $0.50/1k. | $0.50 per 1,000 texts |
| [Dataset Merge & Dedup: Combine Datasets, Remove Duplicates](tools/dataset-dedup-merge.md) · [Run](https://apify.com/ucchiii/dataset-dedup-merge) | Merge Apify datasets and remove duplicate items by any fields (url, placeId, email). Case and URL normalization, keep first or last, export CSV. $0.50/1k. | $0.50 per 1,000 items |

## How to use
1. Open a tool on the Apify Store and click **Try for free** (empty input = free demo).
2. Or call it from code with the Apify API / `apify-client` (Python, JavaScript).
3. Chain them: e.g. a Google Maps scraper → [Maps Leads Cleaner](tools/maps-leads-cleaner.md) → [Dataset Merge & Dedup](tools/dataset-dedup-merge.md).

Questions or feature requests: open an issue in this repository or on the tool's Apify page.
