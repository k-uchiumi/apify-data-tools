# DNS Records Lookup: Bulk A, MX, TXT, SPF, DMARC

**Run it on Apify:** https://apify.com/ucchiii/dns-records-lookup · **Price:** $0.50 per 1,000 domain resolveds · free demo with empty input

**For anyone who has to check DNS for many domains and is tired of running `dig` one name at a time:** IT and migration teams, email deliverability and security people checking SPF / DMARC, agencies that manage client domains, SEO and brand-protection teams. Give this Actor up to 2,000 domains (or a dataset) and get **one flat row per domain: A, AAAA, CNAME, MX, NS, TXT, SOA, CAA and SRV records, the SPF and DMARC policy lines, a "has mail server" flag and the DNSSEC flag**.
No scraping, no proxies, no API keys: it asks the public **DNS-over-HTTPS JSON APIs of Cloudflare and Google** and falls back to the other one when one fails.

**Try it free: leave input empty and press Start** (four example domains, not charged, rows carry `_demo: true`).

## Sample output
(Made-up placeholder values that show the shape of the rows.)

| domain | status | a | mx | spfRecord | dmarcRecord | hasMx | dnssec | resolver |
|---|---|---|---|---|---|---|---|---|
| example.com | ok | 192.0.2.10 | 10 mail.example.com | v=spf1 include:_spf.example.net ~all | v=DMARC1; p=reject | true | true | cloudflare |
| no-such-name.example | nxdomain |  |  |  |  | false | false | cloudflare |

## Price
**$0.50 per 1,000 domains** ($0.0005 each; 1,000 domains cost $0.50, the full 2,000-domain cap costs $1). You pay only for domains that were **resolved**: records came back, or the resolver answered NXDOMAIN (a clear "this name does not exist" answer is a result). Failed lookups, invalid input and the demo are free. The price is the same for one record type or nine. Set a maximum charge per run to cap spend; the Actor stops cleanly when it is reached.

## What it does
- **Nine record types, your choice**: A, AAAA, CNAME, MX (as `"10 mail.example.com"`, sorted by priority), NS, TXT (long records joined), SOA, CAA, SRV. Default: A, AAAA, MX, NS, TXT.
- **Email helpers**: `spfRecord` (the TXT starting with `v=spf1`), `dmarcRecord` (from `_dmarc.<domain>`, can be switched off), `hasMx` (false for a null MX `0 .`).
- **DNSSEC flag**: `dnssec` is the resolver's AD ("authenticated data") flag: `true` when the answers were DNSSEC-validated.
- **Two output formats**: one flat row per domain (a list per type), or one row per DNS record with `domain`, `type`, `name`, `ttl`, `value`.
- **Messy input is fine**: `https://WWW.Example.com/page?x=1` becomes `www.example.com`, internationalized names become punycode, duplicates are removed, typos come back as an `error` row with `invalid_domain` (free). Hostnames and service names such as `_sip._tcp.example.com` work too.
- **Polite and robust**: default 5 domains in parallel (max 10), a minimum gap between requests to the same resolver, timeouts, up to 3 retries with backoff (honours `Retry-After`), then the other resolver is asked. One failing domain never stops the run.

## Input example
```json
{
  "domains": ["example.com", "python.org", "https://www.github.com/"],
  "recordTypes": ["A", "AAAA", "MX", "NS", "TXT"],
  "includeDmarc": true,
  "outputMode": "perDomain",
  "dnsResolver": "cloudflare",
  "concurrency": 5
}
```
Or point it at a dataset of another Actor with `datasetId` and `domainField`.

## Output
One row per domain (`outputMode: perDomain`). Lists appear only for the record types you chose; `spfRecord` needs TXT, `hasMx` needs MX.
```json
{
  "domain": "example.com",
  "status": "ok",
  "a": ["192.0.2.9", "192.0.2.10"],
  "aaaa": ["2001:db8::1"],
  "mx": ["10 mx1.example.com", "20 mx2.example.com"],
  "ns": ["ns1.example.net", "ns2.example.net"],
  "txt": ["v=spf1 include:_spf.example.net ~all"],
  "spfRecord": "v=spf1 include:_spf.example.net ~all",
  "dmarcRecord": "v=DMARC1; p=reject; rua=mailto:dmarc@example.com",
  "hasMx": true,
  "dnssec": true,
  "resolver": "cloudflare",
  "error": null,
  "checkedAt": "2026-10-24T09:00:00Z"
}
```
With `outputMode: perRecord` every record is a row: `{"domain": "example.com", "type": "MX", "name": "example.com", "ttl": 3600, "value": "10 mx1.example.com", "status": "ok", "resolver": "cloudflare"}`. The DMARC record is a TXT row named `_dmarc.example.com`. A name without any record, NXDOMAIN or an error still gets one row with `type` and `value` empty.

| status | meaning | charged |
|---|---|---|
| `ok` | all questions were answered (a name can be `ok` with an empty list: it exists but has no such record) | yes |
| `nxdomain` | the resolver says the name does not exist | yes |
| `error` | a lookup failed after retries and the fallback resolver (see `error`, e.g. `mx:dns_http_503`), or the input is not a valid domain (`invalid_domain`) | no |

If one question of a domain fails, the whole row is `error` (the records that did arrive are still shown) so that you never mistake a failure for "no record". Run those domains again. The record `SUMMARY` in the key-value store counts each status and the charged domains.

## Use cases
- Check SPF and DMARC for a client or supplier list before a deliverability audit
- Find domains with no mail server (`hasMx: false`) or with old MX / NS records before a migration
- Export every record with TTL (`perRecord`) to diff against a zone file
- Spot dangling CNAMEs and missing CAA records across a portfolio
- Enrich a domain list from another scraper with IPs, nameservers and mail providers

## Limits (cannot be raised by input)
- 2,000 domains per run (extra names are ignored with a log warning); up to 10 in parallel
- At most as many connections to one resolver as your concurrency setting, at least 0.05 s between requests to the same resolver
- Each request has a timeout (default 10 s, max 60 s) and is retried at most 3 times per resolver
- The answers are those of Cloudflare's and Google's public resolvers, which cache; TTLs shown are the remaining TTL and can differ between calls. Both resolvers apply their own rate limits; heavy runs may see `error` rows, which are free
- `dnssec` shows whether the resolver validated the answers, not whether the domain's registrar has the right DS record

## FAQ
**Which resolver answers?** The one you choose first (default Cloudflare); if it fails the other one. Each row says which one in `resolver`.
**Why is a subdomain without records `ok` with an empty list?** DNS answers "no data" for that type. NXDOMAIN is only when the name does not exist at all.
**How do I get SRV records?** Add SRV to the types and give the full service name, e.g. `_sip._tcp.example.com`. DKIM works the same way with TXT: give `selector._domainkey.example.com`.
**Does it return WHOIS or registrar data?** No. This Actor is DNS only.

<!-- more-tools:start -->
## More tools from the same developer

- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
- [Domain Availability Checker](https://apify.com/ucchiii/domain-availability-checker) — bulk domain availability check via RDAP: registered or likely available
- [Website Technology Detector](https://apify.com/ucchiii/website-technology-detector) — tech stack of websites in bulk: CMS, frameworks, analytics and tag IDs, CDN, consent tools
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
