# SEO Audit Tool: On-Page Checks for Your URLs

**Run it on Apify:** https://apify.com/ucchiii/seo-audit-tool · **Price:** $6.00 per 1,000 page audits · free demo with empty input

**SEO Audit Tool: give it your URLs (or your sitemap) and get one on-page SEO audit row per page — title and meta description length, h1, canonical, noindex and robots.txt, hreflang, Open Graph, missing alt text, links, JSON-LD, mixed content, redirects and speed — with a list of issues by severity and a 0–100 score. Plain HTTP, no browser, no crawling.**

| inputUrl | score | indexable | issues | title |
|---|---|---|---|---|
| https://apify.com/about | 97 | true | `title_too_short` (low): "Title is 13 characters (under 30); it may not describe the page well." | About · Apify |

*(real output for apify.com/about on 2026-11-12, shortened to 5 columns; the full row also has the meta description (149 characters), h1, canonical (`self`), robots.txt and Googlebot status, Open Graph tags, 67 images (0 without alt), 42 internal and 57 external links, JSON-LD type `Organization`, response time, page size and more)*

**For SEO specialists, agencies, site owners and developers** who need a quick on-page check of a list of pages — after a migration, before a launch, for a client report, or every week from a scheduled run — without setting up a desktop crawler.

**Try it:** press **Start** with the prefilled example (3 pages) and you get 3 rows in a few seconds, for about 2 cents. Clear the inputs and press Start for a **free demo** on 3 sample pages (rows carry `_demo: true`, not charged).

## What it checks
Each page is fetched once and checked for:
- **Indexability**: `noindex` / `none` in the meta robots tag or the `X-Robots-Tag` header (including rules aimed at Googlebot), whether **robots.txt allows Googlebot** to crawl the URL, and whether the **canonical** points to this page, another page, or is missing or duplicated. `indexable` sums it up, and `indexabilityReasons` says why not.
- **Title and meta description**: present, length in characters, duplicates.
- **Headings**: number of h1 (missing, several, empty) and h2.
- **hreflang**: every alternate language link, invalid language codes, and whether the set includes the page itself.
- **Sharing tags**: `og:title`, `og:description`, `og:image` (which are missing) and `twitter:card`.
- **Images**: how many `img` tags have no `alt` attribute (`alt=""` is accepted as a decorative image).
- **Links**: distinct internal links (same host, `www.` ignored), external links and `nofollow` links.
- **Structured data**: JSON-LD blocks, their schema.org `@type` values (also inside `@graph`), and blocks that are not valid JSON.
- **Mobile and language**: meta viewport and the `lang` attribute.
- **Security**: plain-HTTP pages, and **mixed content** — images, scripts, stylesheets, frames or media loaded over `http://` on an `https://` page (with up to 5 examples).
- **Status and speed**: final status code, the full **redirect chain** with each hop's status (chains of 2+ and temporary redirects are flagged), HTML response time and size, and how many words of visible text the HTML has.

## How the score works
Every page starts at **100**. Each issue found takes off its points; the score never goes below 0. These thresholds are our own rules of thumb, not limits published by a search engine.

| Severity | Issue code | Points |
|---|---|---|
| high | `noindex` — meta robots or X-Robots-Tag says noindex | 30 |
| high | `blocked_for_googlebot` — robots.txt disallows the URL for Googlebot | 30 |
| high | `title_missing` | 15 |
| medium | `canonical_points_elsewhere` | 10 |
| medium | `meta_description_missing`, `h1_missing`, `viewport_missing`, `not_https`, `mixed_content`, `canonical_multiple` | 8 each |
| medium | `json_ld_invalid` | 5 |
| low | `images_missing_alt` — 1 point per image without alt, at most 5 | 1–5 |
| low | `title_too_long` (over 60 characters), `title_too_short` (under 30), `title_multiple`, `h1_multiple`, `h1_empty`, `canonical_missing`, `nofollow`, `og_tags_missing`, `redirect_chain` (2+ redirects), `slow_response` (over 2,000 ms), `large_html` (over 1 MB), `thin_content` (under 200 words and 600 characters of text), `hreflang_invalid` | 3 each |
| low | `temporary_redirect` (302/303/307), `hreflang_no_self`, `meta_description_too_long` (over 160), `meta_description_too_short` (under 70), `meta_description_multiple`, `lang_missing`, `json_ld_missing` | 2 each |
| info | `redirected` (one redirect), `truncated` (page over 3 MB, only the first 3 MB checked) | 0 |

Each issue in `issues` has its `code`, `severity`, `points` and a plain-English `message` with the evidence (for example the actual length or the canonical URL). `issueCodes` lists just the codes, most severe first, for easy filtering.

## How to use
1. Put your page URLs in **URLs**, one per line, and/or your sitemap in **Sitemap URLs** (sitemap index files and `.gz` sitemaps are read too).
2. Set **Max pages** (default 100, up to 2,000 per run). URLs come first, then sitemap pages, until the limit.
3. Press **Start**, or call the Actor from your code:

```bash
curl -X POST "https://api.apify.com/v2/acts/ucchiii~seo-audit-tool/run-sync-get-dataset-items?token=<YOUR_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"urls": ["https://apify.com/", "https://apify.com/store"]}'
```

```json
{
  "urls": ["https://apify.com/", "https://apify.com/store"],
  "sitemapUrls": ["https://apify.com/sitemap.xml"],
  "maxPages": 50,
  "concurrency": 5
}
```

Export the dataset as CSV or Excel for a report, or sort by `score` to see the weakest pages first. The **SUMMARY** record counts how many pages have each issue (`pagesByIssue`) and gives the average score.

## Output fields
- `inputUrl`, `source` (`input` or `sitemap`), `finalUrl`, `statusCode`, `score`, `indexable`, `indexabilityReasons`, `highIssues`, `mediumIssues`, `lowIssues`, `issueCodes`, `issues`.
- `title`, `titleLength`, `metaDescription`, `metaDescriptionLength`, `h1`, `h1Count`, `h2Count`.
- `canonical`, `canonicalStatus` (`self`, `other`, `missing`, `multiple`), `canonicalHeader`, `xRobotsTag`, `robotsTxtAllowed`, `googlebotAllowed`.
- `lang`, `viewport`, `hreflang` (lang and url), `hreflangCount`, `ogTitle`, `ogDescription`, `ogImage`, `ogMissing`, `twitterCard`.
- `imagesTotal`, `imagesMissingAlt`, `internalLinks`, `externalLinks`, `nofollowLinks`, `jsonLdCount`, `jsonLdTypes`, `jsonLdErrors`, `mixedContentCount`, `mixedContentSamples`, `wordCount`.
- `redirectCount`, `redirectChain` (url and statusCode of each hop), `responseTimeMs`, `pageBytes`, `truncated`, `contentType`, `charset`, `audited`, `fetchedAt`.
- `error` — `null` when the page was audited; otherwise `blocked_by_robots_txt`, `robots_txt_unavailable`, `invalid_url`, `blocked_private_address`, `timeout`, `dns_failure`, `connection_error`, `tls_error`, `http_404` (or another `http_<status>`), `too_many_redirects`, `redirect_loop`, `invalid_redirect_url` or `not_html`.

## Pricing
Pay per event: **$0.006 per page audit** — a page that was fetched and audited (a row with a score). That is **$6.00 per 1,000 pages** on the free plan; with Store discounts it is $5.40 (BRONZE), $4.80 (SILVER) and **$4.20 per 1,000 (GOLD)**.

**Not charged:** pages skipped because robots.txt disallows them, 4xx/5xx pages, timeouts and other fetch errors, blocked addresses, non-HTML files (PDF, images), invalid URLs, and the free demo (empty input).

On the Apify Free plan, the $5 of monthly credit covers about 830 page audits a month. Set a **maximum charge per run** to cap spend: the run stops cleanly when it is reached.

## Polite and safe by design
- **robots.txt is respected.** Before fetching a page (and every redirect target), the Actor reads that site's robots.txt once and follows the rules for its own name (`seo-audit-tool`) or `*`. Disallowed pages are not fetched and not charged (`error: blocked_by_robots_txt`). If robots.txt cannot be read because of a server error or timeout, the site's pages are not fetched either (`robots_txt_unavailable`, not charged). A missing robots.txt (404) means no restrictions.
- **Gentle on servers**: at most 2 connections per site at a time and at least 0.5 s between requests to the same site; a robots.txt `Crawl-delay` is honoured up to 5 s. At most 2,000 pages per run, 10 redirects per page and 3 MB of HTML per page.
- **Never reaches internal networks**: URLs that resolve to internal addresses (localhost, private ranges, link-local such as 169.254.169.254, IPv6 loopback/unique-local) are never contacted — also when a public URL redirects to one (`error: blocked_private_address`).
- The Actor identifies itself in its User-Agent and does not pretend to be Googlebot; `googlebotAllowed` comes from reading the robots.txt rules.

## Limits
- One request per page; links are not followed (no site crawl). Use your sitemap, or [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls), to list a whole site.
- HTTP only: titles, tags and text that a page adds with JavaScript after loading are not seen, and sites that block bots may answer with an error page.
- Response time is measured from Apify's servers for the HTML only (no images, scripts or rendering); it is not a Core Web Vitals score.
- Only fetch pages you are allowed to access.

## FAQ
**Can it audit a whole website?** Give it the site's sitemap in **Sitemap URLs** and raise **Max pages**. It audits the pages the sitemap lists, up to 2,000 per run; it does not discover pages by following links.

**Why is a page "not indexable" when it returns 200?** A 200 page can still be kept out of search results by `noindex`, by robots.txt rules for Googlebot, or by a canonical that points to another URL. `indexabilityReasons` names which one.

**Do I pay for pages blocked by robots.txt or broken pages?** No. Only rows with `audited: true` are charged.

**Is the score the same as in other SEO tools?** No — every tool weighs issues differently. Our weights are listed in full above, so you can recompute or re-weight the score from `issues`.

<!-- more-tools:start -->
## More tools from the same developer

- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Broken Link Checker](https://apify.com/ucchiii/broken-link-checker) — find broken links on web pages: status of every link found
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [Redirect Checker](https://apify.com/ucchiii/redirect-checker) — full redirect chain for each URL, hop by hop, with loop detection
- [Link Preview API](https://apify.com/ucchiii/link-preview-api) — link previews for URL lists: Open Graph and Twitter Card title, description, image, site name and favicon
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
