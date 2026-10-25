# Bulk HTTP Request: send many API calls from a list

**Run it on Apify:** https://apify.com/ucchiii/bulk-http-request · **Price:** $1.00 per 1,000 request completeds · free demo with empty input

**For developers, data and ops teams who have a list of API calls to make and don't want to write a loop.** Give it
URLs (or full requests with method, headers and body) and get one dataset row per request: **status code, response
headers, JSON or text body, timing and errors**. Use it to call an API for hundreds of IDs, fire webhooks, POST many
records, check endpoints or HEAD a list of URLs. **$1 per 1,000 completed requests.** Leave the input empty for a free demo.

## Use cases
- Call an API once per ID or record (GET/POST/PUT/PATCH/DELETE) and collect every response in a dataset
- Send many webhooks or notifications and see which ones were accepted (2xx) or rejected
- Check endpoints, status codes and response headers with HEAD or GET across a long list
- Fetch many JSON endpoints and get the parsed JSON, ready for a spreadsheet or the next Actor

## How it works
- **Two input styles:** `requests` — a list of `{url, method, headers, body}` objects — or a simple `urls` list that shares one `method`, `headers` and `body`. Per-request values override the shared ones.
- Body can be a string (sent as is) or any JSON value (sent as JSON with `Content-Type: application/json`)
- `concurrency` up to 20, `timeoutSecs`, `retries` (up to 5; POST and PATCH are only retried when no connection was made, so nothing is sent twice) and `followRedirects` (up to 10 hops)
- Response body is cut at `maxBodyBytes` (default 100 KB) and flagged with `bodyTruncated`; `parseJson` returns valid JSON as the `json` field
- Response headers: `selected`, `all` or `none`
- Up to 10,000 requests per run. A per-host limit (5 connections, 0.05 s between starts) keeps it polite
- Run totals are saved as `SUMMARY` in the key-value store

## Safe by design
- **Internal addresses are never contacted.** Hostnames are resolved and anything that points to localhost, private ranges, link-local (such as the cloud metadata address 169.254.169.254), IPv6 loopback/unique-local and similar is refused — also at every redirect hop. Such rows have `error: "blocked_private_address"`
- **Secrets stay out of logs and output.** Logs show only host names. `Authorization`, `Cookie`, API-key and token headers are shown as `[REDACTED]` in the output's `requestHeaders`, and `Set-Cookie` is redacted in response headers. Authorization and Cookie are dropped on redirects to another host
- Note: headers you enter are part of the run input; use your own credentials and keep the run private

## Pricing
**$0.001 per completed request.** A request is completed when the server answers — any HTTP status counts (a 404 or 500 is charged, a retry is not). Network errors (timeouts, DNS failures), invalid requests and requests blocked by the internal-address guard are **not charged**. Free demo on empty input (not charged).

## Input
```json
{
  "requests": [
    { "url": "https://httpbin.org/get", "method": "GET" },
    { "url": "https://httpbin.org/post", "method": "POST", "body": { "hello": "world" } }
  ],
  "headers": { "Accept": "application/json" },
  "parseJson": true,
  "concurrency": 5
}
```
Or `{ "urls": ["https://example.com/a", "https://example.com/b"], "method": "HEAD" }`.

## Output
```json
{
  "index": 1, "url": "https://httpbin.org/post", "method": "POST", "statusCode": 200, "ok": true,
  "headers": { "content-type": "application/json", "content-length": "512" },
  "body": null, "json": { "json": { "hello": "world" } }, "bodyBytes": 512, "bodyTruncated": false,
  "elapsedMs": 431, "finalUrl": "https://httpbin.org/post", "redirectCount": 0, "attempts": 1,
  "requestHeaders": { "Accept": "application/json", "Content-Type": "application/json" }, "error": null
}
```
`ok` is true for a 2xx final status. When no response arrives, `statusCode` is `null` and `error` is e.g. `timeout`,
`dns_failure`, `connection_error: ...`, `too_many_redirects`, `redirect_loop`, `invalid_request` or `blocked_private_address`.
Rows are written as they finish; use `index` to restore the input order.

## Notes
You are responsible for what you send: only call servers and APIs you are allowed to use, and follow their terms of
service and rate limits. The Actor sends exactly the requests you list; it does not crawl or follow links.
Known limit: the internal-address check happens just before connecting; an attacker-controlled DNS server that changes its answer between the check and the connection (DNS rebinding) is not covered.

<!-- more-tools:start -->
## More tools from the same developer

- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Website Technology Detector](https://apify.com/ucchiii/website-technology-detector) — tech stack of websites in bulk: CMS, frameworks, analytics and tag IDs, CDN, consent tools
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [HTML to Markdown Converter for LLM and RAG](https://apify.com/ucchiii/html-to-markdown) — clean Markdown from URLs, raw HTML or datasets for LLM and RAG
- [Dataset Transform](https://apify.com/ucchiii/dataset-transform) — flatten, filter, rename and convert any Apify dataset, export CSV / JSON / JSONL with a link
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
