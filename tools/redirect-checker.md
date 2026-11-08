# Redirect Checker: 301 Redirect Chains, Hop by Hop

**Run it on Apify:** https://apify.com/ucchiii/redirect-checker · **Price:** $0.30 per 1,000 URLs · free demo with empty input

**Redirect checker for lists of URLs: follow every redirect hop by hop and see the full chain.**

Paste URLs (or pick a dataset), press **Start**, and get one row per URL with every hop's HTTP status code and raw `Location` header, the final URL and status, the number of hops, redirect loops, and whether the chain moved from http to https or added or removed `www`. Made for **site migrations** (verify your 301 map), **SEO audits** (find chains, temporary redirects and loops) and checking short links before you publish them.

**Try it free:** leave the input empty and press **Start** — a few sample URLs are checked (not charged, rows carry `_demo: true`).

## What you get

| url | statusChain | hops | finalUrl | httpToHttps | wwwChange | loop |
|---|---|---|---|---|---|---|
| http://example.com/old | 301 > 302 > 200 | 2 | https://www.example.com/new | true | added | false |
| https://shop.example/a | 302 > 307 | 2 | https://shop.example/a | false | null | true |
| https://example.com/ | 200 | 0 | https://example.com/ | false | null | false |

Each row also has the full `chain`:

```json
[
  {"step": 1, "url": "http://example.com/old", "statusCode": 301, "redirectType": "permanent",
   "location": "https://example.com/old", "nextUrl": "https://example.com/old"},
  {"step": 2, "url": "https://example.com/old", "statusCode": 302, "redirectType": "temporary",
   "location": "https://www.example.com/new", "nextUrl": "https://www.example.com/new"},
  {"step": 3, "url": "https://www.example.com/new", "statusCode": 200, "redirectType": null,
   "location": null, "nextUrl": null}
]
```

## How to use
1. **URLs**: full URLs with `http://` or `https://`, one per line — or **a dataset** and the name of its URL field.
2. Optional: **Maximum hops** (default 10), **Request method** (HEAD then GET, or GET only), timeout, concurrency, User-Agent.

```json
{
  "urls": ["http://example.com/old-page", "https://example.com/blog/2019/post"],
  "maxRedirects": 10,
  "method": "auto"
}
```

## Output fields
- `url`, `finalUrl`, `finalStatusCode` — what you asked for and where it ended
- `hops`, `redirected`, `statusChain` (`301 > 302 > 200`) — how long the chain is
- `chain` — every hop: `step`, `url`, `statusCode`, `redirectType` (`permanent` for 301/308, `temporary` for 302/303/307), `location` (the raw header, relative or absolute) and `nextUrl` (resolved)
- `loop` — a redirect pointed back to a URL already in the chain (`error: redirect_loop`)
- `httpToHttps`, `httpsToHttp`, `wwwChange` (`added` / `removed`), `hostChanged`, `hasTemporaryRedirect`
- `responseTimeMs`, `checkedAt`, `error` (`too_many_redirects`, `invalid_redirect_url`, `redirect_to_private_address`, `timeout`, `dns_failure`, ...)
- **STATS** record: URLs checked and charged, redirected, loops, errors, counts by hops and by final status

## Limits (cannot be raised by input)
- Up to 10,000 URLs per run; at most 20 hops per URL; 60 seconds per request at most
- Only HTTP redirects (3xx with a `Location` header). JavaScript and `<meta refresh>` redirects are not followed, because page bodies are never downloaded
- Polite by design: at most 2 connections per host and at least 0.2 s between requests to the same host
- URLs that resolve to internal addresses (localhost, private ranges, 169.254.169.254) are never contacted, also when a redirect points there
- Set a maximum charge per run to cap spend: the run stops cleanly when it is reached and keeps the rows found so far

## Pricing
Pay per event: **$0.0003 per checked URL** ($0.30 per 1,000 URLs), no start fee. A URL is charged only when its chain was followed to the end, to a loop or to the hop limit. Invalid, blocked and unreachable URLs (DNS failure, timeout) and the demo are free.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 16,666 URLs a month.

## FAQ
**Why HEAD first?** It is the lightest request and most servers redirect HEAD the same way as GET. When a server refuses HEAD (405 or 501), the same hop is repeated with GET. Choose **GET only** if a site answers HEAD differently.

**Does it crawl my site?** No. Only the URLs you give and the redirects they answer with are requested.

**Can I check a migration map?** Yes: put the old URLs in, then compare `finalUrl` with the new URLs in a spreadsheet. `hasTemporaryRedirect` and `hops > 1` show the redirects worth fixing.

**I need status codes and response times for all pages, not just redirects.** Use the Bulk URL Status Checker from the same developer.

<!-- more-tools:start -->
## More tools from the same developer

- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Broken Link Checker](https://apify.com/ucchiii/broken-link-checker) — find broken links on web pages: status of every link found
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [Bulk HTTP Request](https://apify.com/ucchiii/bulk-http-request) — send many HTTP requests (GET/POST/...) from a list and collect status, headers and body
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
