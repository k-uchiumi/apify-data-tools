# Domain WHOIS & DNS Lookup: Registrar, Expiry, A/MX/NS

**Run it on Apify:** https://apify.com/ucchiii/domain-whois · **Price:** $0.50 per 1,000 domain looked ups · free demo with empty input

**For anyone who has to check many domains at once and is tired of pasting them one by one into a whois site:** domain investors and brokers, agencies and IT teams that manage client domains, security and brand-protection teams, and SEO people vetting expired or competitor domains. Give this Actor a list of domains (or a dataset) and get **one clean row per domain: registrar, created / updated / expiry dates, days to expiry, status, nameservers and A / MX / NS records**.
**$0.50 per 1,000 domains.** It uses **RDAP** (the modern replacement for port-43 WHOIS, from the IANA bootstrap) and **DNS over HTTPS** (Cloudflare or Google). No scraping, no proxies, no API keys.

## What it does
- **WHOIS data via RDAP**: registrar and its IANA ID, creation date, last-changed date, expiration date, **days to expiry**, `expired` flag, domain status codes (`client transfer prohibited`, …), nameservers, DNSSEC flag
- **DNS records via DNS over HTTPS**: A (IPv4), MX (with priority) and NS records, from Cloudflare (1.1.1.1) or Google Public DNS
- **Registered or not**: unregistered names come back as `not_registered` (and are free), so you can screen a list of names
- **Messy input is fine**: `https://WWW.Example.com/page?x=1` becomes `www.example.com`, internationalized names become punycode, duplicates are removed, typos come back as `invalid` rows (free)
- **TLDs without RDAP** (several country domains): the row is built from DNS and marked `dns_only`
- **Polite and robust**: per-server connection and spacing limits, timeouts, up to 3 retries with backoff (honours `Retry-After`), one failing domain never stops the run

## Privacy: no personal data
Registrant, administrative, technical and billing contacts are **never read or output** — no names, e-mails, phone numbers or addresses. Only the registrar (a company), dates, statuses and nameserver names are used. Every row carries `registrantRedacted: true`. E-mail addresses typed into the input are rejected as invalid, not looked up.
(Many registries redact registrant data anyway since GDPR; this Actor does not depend on that.)

## Input example
```json
{
  "domains": ["example.com", "python.org", "https://www.apify.com/store"],
  "includeDns": true,
  "dnsResolver": "cloudflare",
  "concurrency": 5
}
```
Or point it at a dataset of another Actor with `datasetId` and `domainField`.
**Empty input runs a free demo** on four domains (not charged, rows carry `_demo: true`).

## Output
One row per domain in the dataset (a table view for registration data and one for DNS records are included):
```json
{
  "domain": "example.com",
  "status": "ok",
  "registered": true,
  "registrar": "Example Registrar, Inc.",
  "registrarIanaId": "9999",
  "createdAt": "2001-01-01T00:00:00Z",
  "updatedAt": "2026-01-01T00:00:00Z",
  "expiresAt": "2027-01-01T00:00:00Z",
  "daysToExpiry": 100,
  "expired": false,
  "domainStatus": ["client transfer prohibited"],
  "nameservers": ["ns1.example.net", "ns2.example.net"],
  "dnssecSigned": false,
  "dnsA": ["192.0.2.1"],
  "dnsMx": ["10 mail.example.com"],
  "dnsNs": ["ns1.example.net", "ns2.example.net"],
  "rdapServer": "rdap.example-registry.test",
  "rdapDomain": "example.com",
  "registrantRedacted": true,
  "error": null,
  "checkedAt": "2026-10-21T09:00:00Z"
}
```
(Made-up placeholder values that show the shape of a row.) `status` is one of:
| status | meaning | charged |
|---|---|---|
| `ok` | RDAP registration data returned | yes |
| `dns_only` | the TLD has no RDAP service; DNS records returned | yes |
| `not_registered` | the registry says the name is not registered | no |
| `partial` | RDAP failed (see `error`) but DNS answered | no |
| `invalid` | not a valid domain name | no |
| `error` | the lookup failed (timeout, rate limit, server error; see `error`) | no |

The record `SUMMARY` in the key-value store counts each status and the number of charged domains.

## Use cases
- Watch a portfolio of client or brand domains for upcoming expiry (`daysToExpiry` below 30)
- Screen thousands of candidate names: which are registered, since when, with which registrar
- Find which domains still point to old nameservers or mail servers before a migration
- Enrich a list of domains from another scraper with registrar, age and DNS data

## Limits (cannot be raised by input)
- 50,000 domains per run; up to 10 in parallel; at most 2 connections to one server at a time, at least 0.2 s between requests to the same server
- Each request has a timeout (default 15 s, max 60 s) and is retried at most 3 times
- RDAP data comes from the registry or registrar of each TLD and can differ slightly between them (some omit the updated date, for example). Servers may answer with rate limits under heavy load; those domains come back as `error` and are not charged, so you can run them again
- Expiry dates are the registry's; a domain in a grace period can show a past date (`expired: true`)

## Pricing
Pay per event: **$0.0005 per domain looked up** ($0.50 per 1,000). Unregistered, invalid, partial and failed lookups are free. Set a maximum charge per run to cap spend; the Actor stops cleanly when it is reached.

## FAQ
**Is this the same as WHOIS?** It returns the same kind of registration data, through RDAP, the protocol ICANN requires registries and registrars to run. It does not connect to port-43 WHOIS servers.
**Why is there no owner name?** By design (see Privacy). The Actor is for domain status, expiry and DNS checks, not for finding people.
**Why `dns_only` for some domains?** A few country TLDs publish no RDAP service. You still get the DNS records and a registered flag.
**Can I check subdomains?** Yes. DNS records are those of the exact name you give. Registration data belongs to the registered domain, so for `www.example.com` the Actor looks up `example.com` and shows it in `rdapDomain`. (It has no public-suffix list: for names under multi-part suffixes such as `co.uk`, give the registered domain itself.)

<!-- more-tools:start -->
## More tools from the same developer

- [DNS Records Lookup](https://apify.com/ucchiii/dns-records-lookup) — bulk DNS lookup: A, AAAA, MX, NS, TXT, SPF and DMARC per domain
- [Domain Availability Checker](https://apify.com/ucchiii/domain-availability-checker) — bulk domain availability check via RDAP: registered or likely available
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Website Technology Detector](https://apify.com/ucchiii/website-technology-detector) — tech stack of websites in bulk: CMS, frameworks, analytics and tag IDs, CDN, consent tools
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
