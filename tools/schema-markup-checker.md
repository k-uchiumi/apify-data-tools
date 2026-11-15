# Schema Markup Checker: JSON-LD for URL Lists

**Run it on Apify:** https://apify.com/ucchiii/schema-markup-checker · **Price:** $3.00 per 1,000 URLs · free demo with empty input

**Schema Markup Checker: give it a list of URLs and get one row per page with the JSON-LD structured data it carries — every schema.org type found, whether the key fields of Organization, Article, FAQPage, LocalBusiness, BreadcrumbList, Event and a few more types are present, and which JSON-LD blocks are broken JSON — each issue as a short English sentence with the evidence. Plain HTTP, no browser, no crawling.**

| inputUrl | types | issues |
|---|---|---|
| https://apify.com/ | Organization, ImageObject, PostalAddress, ContactPoint, WebSite, SoftwareApplication, Offer, CreativeWork | `website_missing_recommended` (low): "WebSite in block 2 has no potentialAction." |

*(real output for apify.com on 2026-11-12, shortened to 3 columns; the full row also has 3 JSON-LD blocks (all valid), the field check for each Organization and WebSite node, response time, page size and more)*

**For SEO specialists, agencies, site owners and developers** who need to know which pages carry structured data and whether it is intact — after a template change, before a launch, for a client report, or every week from a scheduled run — without opening each page in a testing tool by hand.

**Try it:** press **Start** with the prefilled example (3 pages) and you get 3 rows in a few seconds, for less than a cent. Clear the input and press Start for a **free demo** on 3 sample pages (rows carry `_demo: true`, not charged).

## What it checks
Each URL is fetched once. From the HTML it reads:
- **JSON-LD**: every `<script type="application/ld+json">` block. A block that is not valid JSON (trailing comma, unquoted key, truncated text, ...) is flagged with the parser's message and its line and column. Empty blocks, blocks without `@type` and blocks without `@context` are flagged too.
- **Types found**: every schema.org `@type` in the valid blocks, also inside `@graph` and nested objects. Forms such as `https://schema.org/Article` and `schema:Article` are read as `Article`.
- **Microdata**: the schema.org type names in `itemtype` attributes. Only the names are read; microdata fields are not checked.
- **Key fields**, for the top-level nodes of these types (and their common subtypes, for example `NewsArticle` and `BlogPosting` use the Article rules, `Restaurant` and `Dentist` the LocalBusiness rules):

| Type | Required (a missing one is a medium issue) | Recommended (a missing one is a low issue) |
|---|---|---|
| Organization | name, url | logo, sameAs, contactPoint |
| Article | headline, author, datePublished | image, dateModified, publisher |
| FAQPage | mainEntity, and each question has a name and an accepted answer text | |
| LocalBusiness | name, address | telephone, url, openingHoursSpecification or openingHours, geo, image |
| BreadcrumbList | itemListElement, and each item has a position and a name | |
| Event | name, startDate, location | endDate, eventStatus, image, description |
| WebSite | name (or alternateName), url | potentialAction |
| Person | name | url, sameAs, jobTitle |
| VideoObject | name, thumbnailUrl, uploadDate | description, contentUrl or embedUrl, duration |
| JobPosting | title, description, datePosted, hiringOrganization | jobLocation, validThrough, employmentType |
| HowTo | name, step | image, totalTime |
| WebPage | name | description, url |

These field lists are our own rules of thumb, based on the schema.org vocabulary and common usage. They are not the requirements of any search engine's rich-result programme, and a page can follow them and still not get a rich result. "Present" means the value is not empty; a reference such as `{"@id": "#me"}` counts as present.

**Product, Offer and Review markup is not validated.** Those types are listed in `types` when a page has them, and that is all: no field checks, no issues.

## Issues
Each issue in `issues` has a `code`, a `severity` and a plain-English `message` with the evidence (block number, the missing field names, the position of a JSON error). `issueCodes` lists just the codes, most severe first, for easy filtering.

| Severity | Codes |
|---|---|
| high | `json_ld_invalid` (a block is not valid JSON), `no_valid_structured_data` (every block is empty or invalid and there is no microdata) |
| medium | `no_structured_data`, `<type>_missing_required` (for example `organization_missing_required`), `faqpage_incomplete_question`, `breadcrumblist_incomplete_item` |
| low | `<type>_missing_recommended`, `json_ld_empty`, `json_ld_no_type`, `json_ld_no_context` |
| info | `microdata_not_checked` (types found only as microdata), `truncated` (page over 3 MB, only the first 3 MB read) |

## How to use
1. Put your page URLs in **URLs**, one per line.
2. Set **Max URLs** (default 200, up to 5,000 per run). URLs over the limit are left out and counted in the SUMMARY record.
3. Press **Start**, or call the Actor from your code:

```bash
curl -X POST "https://api.apify.com/v2/acts/ucchiii~schema-markup-checker/run-sync-get-dataset-items?token=<YOUR_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"urls": ["https://apify.com/", "https://apify.com/store"]}'
```

```json
{
  "urls": ["https://apify.com/", "https://apify.com/store"],
  "maxUrls": 50,
  "concurrency": 5
}
```

Export the dataset as CSV or Excel, or filter on `issueCodes` to find the pages with broken JSON-LD. The **SUMMARY** record counts the URLs per schema.org type (`urlsByType`) and per issue (`urlsByIssue`), and how many URLs have structured data or invalid JSON-LD.

## Output fields
- `inputUrl`, `finalUrl`, `statusCode`, `highIssues`, `mediumIssues`, `lowIssues`, `issueCodes`, `issues`.
- `types` (all schema.org types in valid JSON-LD), `microdataTypes`, `typeChecks` (per node: `type`, `checkedAs`, `block`, `fieldsFound`, `missingRequired`, `missingRecommended`).
- `jsonLdBlocks`, `jsonLdValid`, `jsonLdInvalid`, `jsonLdErrors` (block number and error text).
- `redirectCount`, `responseTimeMs`, `pageBytes`, `truncated`, `attempts`, `contentType`, `charset`, `audited` (true when the page was checked), `fetchedAt`.
- `error` — `null` when the page was checked; otherwise `blocked_by_robots_txt`, `robots_txt_unavailable`, `invalid_url`, `blocked_private_address`, `timeout`, `dns_failure`, `connection_error`, `tls_error`, `http_404` (or another `http_<status>`), `too_many_redirects`, `redirect_loop`, `invalid_redirect_url` or `not_html`.

## Pricing
Pay per event: **$0.003 per URL check** — a URL that was fetched as HTML and checked (a row with types and issues, also when the page has no structured data). That is **$3.00 per 1,000 URLs** on the free plan; with Store discounts it is $2.70 (BRONZE), $2.40 (SILVER) and **$2.10 per 1,000 (GOLD)**. There is no start fee.

**Not charged:** URLs skipped because robots.txt disallows them, 4xx/5xx pages, timeouts and other fetch errors, blocked addresses, non-HTML files (PDF, images), invalid URLs, and the free demo (empty input).

On the Apify Free plan, the $5 of monthly credit covers about 1,600 URL checks a month. Set a **maximum charge per run** to cap spend: the run stops cleanly when it is reached.

## Polite and safe by design
- **robots.txt is respected.** Before fetching a page (and every redirect target), the Actor reads that site's robots.txt once and follows the rules for its own name (`schema-markup-checker`) or `*`. Disallowed pages are not fetched and not charged (`error: blocked_by_robots_txt`). If robots.txt cannot be read because of a server error or timeout, the site's pages are not fetched either (`robots_txt_unavailable`, not charged). A missing robots.txt (404) means no restrictions.
- **Gentle on servers**: at most 2 connections per site at a time and at least 0.5 s between requests to the same site; a robots.txt `Crawl-delay` is honoured up to 5 s. Timeouts, connection errors and 429/502/503/504 answers are retried at most twice, after a wait (1 s, then 2 s; a `Retry-After` header is followed up to 10 s). At most 5,000 URLs per run, 10 redirects per page and 3 MB of HTML per page.
- **Never reaches internal networks**: URLs that resolve to internal addresses (localhost, private ranges, link-local such as 169.254.169.254, IPv6 loopback/unique-local) are never contacted — also when a public URL redirects to one (`error: blocked_private_address`).
- The Actor identifies itself in its User-Agent and does not pretend to be Googlebot or any other crawler.

## Limits
- One request per URL; links are not followed (no site crawl). Use [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) to list the pages of a site first.
- HTTP only: markup that a page adds with JavaScript after loading is not seen, and sites that block bots may answer with an error page.
- JSON-LD and microdata type names only. RDFa and the `typeof` attribute are not read, and JSON-LD loaded from a separate file is not followed.
- Not a rich-result test: it does not tell you whether a search engine will show a rich result for the page.
- Only fetch pages you are allowed to access.

## FAQ
**Does it check Product, Offer or Review markup?** No. If a page has them, the type names appear in `types`, but their fields are not checked and they produce no issues.

**Do I pay for a page that has no structured data?** Yes, if it was fetched as HTML and checked: the answer "none found" is the result. Pages that could not be fetched, were blocked by robots.txt or are not HTML are free.

**Why is a field reported missing when it is on the page?** Only JSON-LD fields are checked, and only on top-level nodes (the block itself or the items of its `@graph`). A field that sits only in microdata, or only in an object nested inside another type, is not counted.

**Can it check a whole website?** Give it the URLs of the pages (for example from a sitemap export); it does not discover pages by following links.

<!-- more-tools:start -->
## More tools from the same developer

- [SEO Audit Tool](https://apify.com/ucchiii/seo-audit-tool) — on-page SEO audit for URL lists and sitemaps: title, meta description, h1, canonical, noindex, robots.txt, issues and a 0-100 score
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [Link Preview API](https://apify.com/ucchiii/link-preview-api) — link previews for URL lists: Open Graph and Twitter Card title, description, image, site name and favicon
- [Broken Link Checker](https://apify.com/ucchiii/broken-link-checker) — find broken links on web pages: status of every link found
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
