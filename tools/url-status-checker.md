# Bulk URL Status Checker: HTTP Codes, Redirects, 404s

**Run it on Apify:** https://apify.com/ucchiii/url-status-checker · **Price:** $0.30 per 1,000 URLs · free demo with empty input

**For SEO teams and web developers who need to verify a long list of URLs.** Get the HTTP status, the **full redirect
chain**, the final URL and the response time for each — for **site migrations** (verify your 301 map), SEO audits and
**broken-link checks**. **$0.30 per 1,000 URLs checked.** Leave the input empty for a free demo.

## Use cases
- Verify a 301 redirect map after a migration
- Find broken links (404, DNS failures, timeouts) in a URL list or sitemap export
- Detect redirect chains and loops, and http-to-https redirects

- Checks **only the URLs you provide** — it does not crawl pages or follow links
- Redirects are followed manually (up to 10) and every hop is recorded: `redirectChain`
- Tries `HEAD` first and falls back to `GET` when the server answers 405/501
- DNS failures, timeouts and connection errors are reported as `error` (status `null`) instead of failing the run
- Input as a list, or from another Actor's **dataset** (choose the URL field)
- Polite by design: max 2 connections per host, at least 0.2 s between requests to the same host, up to 100,000 URLs per run
- Status counts are saved as `SUMMARY` in the key-value store
- Rows that never reach the network (`invalid_url`, `blocked_private_address`) are **not charged**. Free demo on empty input (not charged)
- Safe by design: URLs that resolve to internal addresses (localhost, private ranges, link-local such as 169.254.169.254, IPv6 loopback/unique-local) are never contacted, including redirect targets — reported as `blocked_private_address`

## Input
```json
{ "urls": ["https://example.com/old-page", "http://example.com"], "followRedirects": true, "timeoutSecs": 15, "concurrency": 5 }
```
Or `{ "datasetId": "<dataset id>", "urlField": "url" }`.

## Output
```json
{
  "url": "http://example.com/old", "finalUrl": "https://example.com/new", "statusCode": 301, "finalStatusCode": 200,
  "ok": true, "redirectCount": 2,
  "redirectChain": [{"url": "http://example.com/old", "status": 301}, {"url": "https://example.com/old", "status": 301}, {"url": "https://example.com/new", "status": 200}],
  "isHttpsRedirect": true, "responseTimeMs": 412, "contentType": "text/html; charset=utf-8", "contentLength": null,
  "error": null, "checkedAt": "2026-10-18T09:00:00+00:00"
}
```
`ok` is true when the final status is 2xx. For unreachable URLs `statusCode` and `finalStatusCode` are `null` and `error`
is e.g. `dns_failure`, `timeout`, `too_many_redirects`, `redirect_loop`, `invalid_url` or `blocked_private_address`.

## Notes
Some sites block automated requests or answer HEAD/GET differently than a browser; results reflect what a plain HTTP client sees.
Set a custom `userAgent` if needed, and only check URLs you are allowed to request.

<!-- more-tools:start -->
## More tools from the same developer

- [Bulk HTTP Request](https://apify.com/ucchiii/bulk-http-request) — send many HTTP requests (GET/POST/...) from a list and collect status, headers and body
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [Website Screenshot API](https://apify.com/ucchiii/website-screenshot) — screenshots and PDFs of web pages: full page, mobile, element, no cookie banners
- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
- [RAG Chunker](https://apify.com/ucchiii/rag-chunker) — split scraped pages into chunks for RAG and vector stores
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
