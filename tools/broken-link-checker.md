# Broken Link Checker: Find Dead Links on Your Pages

**Run it on Apify:** https://apify.com/ucchiii/broken-link-checker · **Price:** $0.65 per 1,000 checked links · free demo with empty input

**Broken link checker for the pages you choose: every link on them, checked and returned as one row with its status.**

Paste the URLs of your pages, press Start, and get a table of every `<a href>` link on those pages (and, if you want, every image): the HTTP status code, whether the link is broken, where it redirects, the anchor text and the page it was found on. Only the pages you list are read: the Actor does not crawl your whole site, so a run is quick and you pay only for the links you asked about.

**Try it free:** leave the page list empty and press **Start**: a built-in sample page with a few real links (one of them a 404) is checked, not charged, and the rows carry `_demo: true`.

## What you get

| sourcePage | linkUrl | anchorText | statusCode | broken | redirectTarget | error |
|---|---|---|---|---|---|---|
| https://example.com/blog/ | https://example.com/pricing | Pricing | 200 | false | | |
| https://example.com/blog/ | https://example.com/old-guide | Read the guide | 404 | true | | |
| https://example.com/blog/ | http://partner.example.org/ | Our partner | 301 | false | https://partner.example.org/ | |
| https://example.com/blog/ | https://gone.example.net/ | Old tool | | true | | dns_failure |

- **Checks the way a browser would:** each link gets a `HEAD` request; when the server answers `HEAD` with 400 or more (many servers do this wrongly), the link is checked again with `GET` before it is called broken. Redirects are followed (up to 10) and the final target is shown.
- **One row per link:** with *List each link once* (default) a menu link that is on all 30 of your pages is one row, and `foundOnPages` lists the 30 pages. Turn it off for one row per page and link.
- **Polite:** at most 2 connections to the same host at a time, at least 0.25 s between requests to it, and each link is requested only once per run.

## How to use

1. **Pages to check**: full URLs, one per line (up to 100).
2. Optional: **Only links to the same site** (internal links only), **Also check images**, **Max links per page**, **Timeout**.
3. Press **Start**. Filter the dataset on `broken = true`, or download it as CSV or Excel.

```json
{
  "startUrls": ["https://example.com/", "https://example.com/blog/"],
  "sameSiteOnly": false,
  "includeImages": true,
  "dedupeLinks": true,
  "maxLinksPerPage": 500
}
```

## Output fields

- `sourcePage`: the page (as you gave it) where the link was found; `foundOnPages` and `occurrences` list every page with that link.
- `linkUrl`: the absolute link target, resolved against the page and its `<base href>`, without the `#fragment`.
- `anchorText`: the visible link text (or the alt text of an image inside the link). `linkType`: `link` or `image`. `internal`: same host as the page.
- `statusCode` (first answer), `finalStatusCode` (after redirects), `broken` (true when the final status is 400 or higher or the request failed), `redirectTarget`, `redirectCount`.
- `error`: `timeout`, `dns_failure`, `connection_error`, `ssl_error`, `too_many_redirects`, `redirect_loop`, `invalid_url` or `blocked_private_address`. `note`: a hint for 401/403/429/999 answers, which often mean the site refuses automated checks rather than a dead link.
- `responseTimeMs`, `checkedAt`. A **STATS** record lists every page read (or why it failed) and the run totals.

Skipped and never checked: `mailto:`, `tel:`, `javascript:` and `data:` links and links to a place on the same page (`#top`).

## Limits (cannot be raised by input)

- 100 pages per run, 1,000 links per page (so at most 100,000 links per run), 5 MB of HTML per page.
- Pages and links are requested only on public addresses: links to internal network addresses (localhost, private ranges, cloud metadata) are reported but not requested, and not charged.
- Pages that need a login, or that build their links with JavaScript after loading, show only the links in the delivered HTML.
- Set a maximum charge per run to cap spend: the Actor stops cleanly at that number of links.

## Pricing

Pay per event: **$0.00065 per checked link** ($0.65 per 1,000 links), whether the link works or is broken. Links that were not requested (internal addresses, invalid URLs), pages that failed to load and the demo are free.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 7,600 checked links a month.

## FAQ

**Does it crawl my whole website?** No. Only the pages you list are read. To check a whole site, give it the list of its pages (for example from your sitemap).

**Why is a link marked broken although it opens in my browser?** Some sites answer automated requests with 403, 429 or 999 (bot protection). These rows have a `note`; open the link to confirm.

**Are links checked more than once?** No. A link that appears on many of your pages is requested once per run.

**Can I check only the broken links later?** Filter the dataset on `broken = true` in Apify Console, or with the Filter Rows Actor.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Redirect Checker](https://apify.com/ucchiii/redirect-checker) — full redirect chain for each URL, hop by hop, with loop detection
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [Website Technology Detector](https://apify.com/ucchiii/website-technology-detector) — tech stack of websites in bulk: CMS, frameworks, analytics and tag IDs, CDN, consent tools
- [SEO Audit Tool](https://apify.com/ucchiii/seo-audit-tool) — on-page SEO audit for URL lists and sitemaps: title, meta description, h1, canonical, noindex, robots.txt, issues and a 0-100 score
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
