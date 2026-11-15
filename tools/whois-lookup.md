# WHOIS Lookup: Bulk Registrar & Expiry Date (RDAP)

**Run it on Apify:** https://apify.com/ucchiii/whois-lookup · **Price:** $1.50 per 1,000 looked-up domains · free demo with empty input

**WHOIS lookup in bulk: registrar, creation and expiry dates, days to expiry, status codes, nameservers and DNSSEC for up to 1,000 domains per run — straight from the registries over RDAP.**

> **Acceptable use:** Follow the terms of use of the registries' RDAP services. Do not use the data for spam or bulk marketing, and do not use it to register domains that contain someone else's trademark or brand.

Paste a list of domains and get **one row per domain** with the registration data a WHOIS search shows — without the personal data. The Actor asks each registry directly through **RDAP** (the official, structured successor of WHOIS): no scraping of WHOIS websites, no proxies, no API keys.

**Try it free:** leave the input empty and press **Start** — three domains are looked up as a demo (not charged, rows carry `_demo: true`).

## What you get

| domain | status | registrar | createdAt | expiresAt | daysToExpiry | nameservers | dnssecSigned |
|---|---|---|---|---|---|---|---|
| example-shop.com | found | Example Registrar, Inc. | 2012-05-14T09:00:00Z | 2027-05-14T09:00:00Z | 191 | ns1.example-dns.net, ns2.example-dns.net | false |
| example-charity.org | found | Sample Registry Services | 2004-02-01T00:00:00Z | 2026-12-01T00:00:00Z | 27 | a.ns.example.org, b.ns.example.org | true |
| surely-not-registered-9f3k2.com | not_found | | | | | | |
| something.nordf | unknown | | | | | | |

*(illustrative rows showing the output format; the free demo shows real results)*

- **Renewal tracking**: `expiresAt` and `daysToExpiry` (negative when expired) for every registered name — sort by days left and renew in time.
- **Who manages it**: registrar name and IANA registrar ID, registry status codes such as `client transfer prohibited`, nameservers and DNSSEC.
- **Messy input is fine**: URLs are cut down to the host name, `www.` and other subdomains use their registered domain's record, duplicates are removed, typos come back as free `invalid` rows.

## How to use
1. **Domains**: one per line (up to 1,000 per run).
2. Optional: **Concurrency** (1–5, default 3) and **Timeout** per request (default 15 s).

```json
{
  "domains": ["apify.com", "python.org", "example.com"],
  "concurrency": 3
}
```

## Output fields
- `domain` — the name as looked up (punycode for international names).
- `status` — `found` (registration data returned), `not_found` (the registry reports no such domain — usually not registered), `unknown` (no answer: the TLD has no RDAP service, or a timeout or rate limit — run it again later), `invalid` (not a domain name).
- `registered` — `true` for `found`, `false` for `not_found`, empty otherwise.
- `registrar`, `registrarIanaId` — the registrar company and its IANA ID.
- `createdAt`, `updatedAt`, `expiresAt` — ISO dates in UTC; `daysToExpiry` and `expired`.
- `domainStatus` — registry status codes (EPP status); `nameservers`; `dnssecSigned`.
- `rdapServer`, `rdapDomain` — which registry answered, and for which name (the parent domain for subdomains).
- `registrantRedacted` — always `true`; `error` — why a row is `unknown` or `invalid`; `checkedAt`.
- **STATS** record: counts of found, not found, unknown, invalid and charged domains.

## Privacy: no personal data
Registrant, administrative, technical and billing contacts are **never read or returned** — no names, e-mails, phone numbers or addresses, even where a registry publishes them. Only the registrar (a company), dates, status codes, nameservers and DNSSEC are used. E-mail addresses typed into the input are rejected as invalid, not looked up.

## What it does not do
No DNS records beyond the nameservers in the registry record. For A, MX, TXT, SPF or DMARC records use the DNS Records Lookup or Domain WHOIS & DNS Lookup Actors from the same developer. For "is this name free to register?" use the Domain Availability Checker, which also double-checks DNS before saying "available".

## Limits (cannot be raised by input)
- **1,000 domains per run**; concurrency 1–5.
- **Polite to the registries**: at most 2 connections and at least 0.25 s between requests per registry server; rdap.org (used only for TLDs missing from the IANA list) at most 1 request per 1.1 s. Rate limits (HTTP 429) and server errors are retried up to 3 times with a growing pause, honouring `Retry-After`.
- TLDs without an RDAP service (for example many country codes) come back `unknown` and are free.
- `.uk` domains (including `.co.uk`) are not looked up: the .uk registry's terms forbid commercial use of its data. They come back `unknown` with `error: tld_excluded_by_registry_terms` and are free.
- Set a **maximum charge per run** to cap spend: the Actor checks it before starting, never looks up more domains than it can charge for and stops cleanly when it is reached.

## Pricing
Pay per event: **$0.0015 per looked-up domain** — a domain the registry answered for (`found` or `not_found`). That is **$1.50 per 1,000 domains**, with no start fee.
`invalid` and `unknown` rows and the demo are **free**.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 3,300 domains a month.

## FAQ
**Is RDAP the same as WHOIS?** RDAP is the official replacement for the old WHOIS protocol, run by the same registries. It returns the same registration data in a structured form; ICANN requires it for all generic TLDs.

**Why is the registrant missing?** By design. Since GDPR most registries redact it anyway, and this Actor never collects personal data.

**Why does a domain I know exists show `not_found`?** Some names are managed below the registry that answered (for example a university name under a country's academic zone): the parent registry has no record for them. Check such names with the Domain WHOIS & DNS Lookup Actor, which also looks at DNS.

**Why `unknown`?** The TLD has no RDAP service, or the registry did not answer in time or rate-limited the request. Unknown rows are free; run them again later.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
- [Domain Age Checker](https://apify.com/ucchiii/domain-age-checker) — how old is a domain: registration date, age in years and days and an age band for up to 1,000 domains, oldest first
- [Domain Availability Checker](https://apify.com/ucchiii/domain-availability-checker) — bulk domain availability check via RDAP: registered or likely available
- [DNS Lookup](https://apify.com/ucchiii/dns-records-lookup) — bulk DNS lookup: A, AAAA, MX, NS, TXT, SPF and DMARC per domain
- [Website Technology Detector](https://apify.com/ucchiii/website-technology-detector) — tech stack of websites in bulk: CMS, frameworks, analytics and tag IDs, CDN, consent tools
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
