# Domain Expiry Checker: Domain Expiration Dates in Bulk

**Run it on Apify:** https://apify.com/ucchiii/domain-expiry-checker · **Price:** $0.50 per 1,000 domain expiry dates · free demo with empty input

**Domain expiry checker for renewal management: paste your own and your clients' domains and get every expiry date and the days left, sorted soonest first, straight from the registries over RDAP.**

| domain | expiresAt | daysToExpiry | expired | registrar | domainStatus | nameservers |
|---|---|---|---|---|---|---|
| client-shop.com | 2026-11-02T09:00:00Z | -4 | true | Example Registrar, Inc. | client transfer prohibited | ns1.example-dns.net, ns2.example-dns.net |
| our-brand.org | 2026-11-20T00:00:00Z | 14 | false | Sample Registry Services | client delete prohibited | a.ns.example.org, b.ns.example.org |
| agency-site.com | 2027-05-14T09:00:00Z | 189 | false | Example Registrar, Inc. | client transfer prohibited | ns1.example-dns.net, ns2.example-dns.net |
| client-shop.co.uk | | | | | | |

*(illustrative rows showing the output format; the `.uk` row comes back without a date and is free — see Limits. The free demo shows real results.)*

Prevent missed renewals for your own and your clients' domains: one run gives you the whole portfolio in one table, the most urgent names at the top. Each registry is asked directly through **RDAP** (the official, structured successor of WHOIS): no scraping, no proxies, no API keys, and no personal data.

**Try it free:** leave the input empty and press **Start** — four sample domains are looked up as a demo (not charged, rows carry `_demo: true`).

> **Acceptable use:** This Actor is for managing the renewals of domains you own or look after. It only checks the list you give it (up to 500 per run). Follow the terms of use of the registries' RDAP services and do not use the data for marketing or spam.

## What you get
- **Soonest first**: rows are sorted by `daysToExpiry`; names already past their expiry date come first (negative days), rows without a date come last.
- **Only what needs attention**: set **Within days** (for example `30`) to keep only domains expiring in the next 30 days. Domains outside the window are left out and not charged.
- **Who to call**: registrar name and IANA registrar ID, registry status codes such as `client transfer prohibited` or `client hold`, and nameservers.
- **Messy input is fine**: URLs are cut down to the host name, duplicates are removed, typos come back as free `invalid_domain` rows.

## How to use
1. **Your domains**: one per line (up to 500 per run), or a **Dataset ID** whose items have a `domain` or `url` field.
2. Optional: **Only domains expiring within N days**, **Concurrency** (1–5, default 3), **Timeout** per request (default 15 s).
3. Schedule the run weekly or monthly in Apify and connect the dataset to e-mail, Slack or Google Sheets for a renewal reminder.

```json
{
  "domains": ["python.org", "apify.com", "example.com"],
  "withinDays": 60
}
```

## Output fields
- `domain` — the name as given (punycode for international names; subdomains use their registered domain's record).
- `expiresAt` — expiry date from the registry (ISO, UTC); `daysToExpiry` — whole days left (negative when past); `expired`.
- `registrar`, `registrarIanaId` — the registrar company and its IANA ID.
- `domainStatus` — registry status codes (EPP status); `nameservers`.
- `checkedAt` — time of the lookup; `error` and `note` — why a row has no date (for example `not_registered`, `no_rdap_for_tld`, `no_expiry_date_published`, `uk_registry_not_queried`, `invalid_domain`, or a timeout / rate limit).
- **SUMMARY** record: domains given and checked, returned with and without a date, past expiry, left out by the filter, charged, and anything not checked with the reason.

## Privacy: no personal data
Registrant, administrative, technical and billing contacts are **never read or returned** — no names, e-mails, phone numbers or addresses. Only the registrar (a company), dates, status codes and nameservers are used. E-mail addresses typed into the input are rejected as invalid, not looked up.

## Limits (cannot be raised by input)
- **500 domains per run.** Domains beyond 500 are not checked; the status message and the SUMMARY record say how many were skipped. Split bigger portfolios into several runs.
- **Your list only**: the Actor does not discover or list domains by itself.
- **Polite to the registries**: at most 2 connections and at least 0.25 s between requests per registry server; rdap.org (used only when the IANA list cannot be loaded) at most 1 request per 1.1 s. Rate limits (HTTP 429) and server errors are retried up to 3 times with a growing pause, honouring `Retry-After`.
- **`.uk` names are not looked up** (including `.co.uk`): the .uk registry's terms forbid commercial copying of its RDAP data. They come back without a date, with `error: uk_registry_not_queried`, and are free. The same applies to the other TLDs whose RDAP service is run by the same registry operator (for example `.wales`, `.cymru`, `.bbc`).
- TLDs without an RDAP service (for example `.io`, `.co`, `.de`, `.eu`, `.jp` at the time of writing) and registries that publish no expiry date come back without a date and are free.
- Set a **maximum charge per run** to cap spend: the Actor checks it before starting and never charges more; with the N-days filter on, the soonest domains are returned first.

## Pricing
Pay per event: **$0.0005 per domain expiry date** — one domain returned with an expiry date from its registry. That is **$0.50 per 1,000 domains**, with no start fee.
Rows without a date (invalid, unregistered, no RDAP, `.uk`, timeouts), domains left out by the N-days filter, and the demo are **free**.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 10,000 domains a month.

## FAQ
**Is RDAP the same as WHOIS?** RDAP is the official replacement for the old WHOIS protocol, run by the same registries. ICANN requires it for all generic TLDs.

**Why is a domain I own missing its date?** Either its TLD has no public RDAP service, the registry does not publish expiry dates (several country-code registries), it is a `.uk` name, or the registry did not answer in time. The `note` column says which. These rows are free; check them with your registrar.

**Which registries' terms have been checked?** The terms of use of Verisign (`.com`, `.net`), Public Interest Registry (`.org`), Identity Digital (`.info`, `.ai` and several hundred other TLDs), Google Registry (`.app`, `.dev` and others), Nominet (`.uk`), rdap.org and the IANA bootstrap list were read in the original. For other TLDs the registry's own RDAP terms have **not been confirmed**; they generally allow lawful, query-based use, but follow each registry's terms when you reuse the data.

**Why is the registrant missing?** By design: this Actor never collects personal data.

**Can it remind me?** Schedule it in Apify (for example every Monday with `withinDays: 30`) and add an integration that sends the dataset to e-mail or Slack.

<!-- more-tools:start -->
## More tools from the same developer

- [SSL Certificate Expiry Checker](https://apify.com/ucchiii/ssl-certificate-checker) — SSL/TLS certificate expiry dates, issuer and validity for many hosts, soonest first
- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
- [Domain Availability Checker](https://apify.com/ucchiii/domain-availability-checker) — bulk domain availability check via RDAP: registered or likely available
- [DNS Lookup](https://apify.com/ucchiii/dns-records-lookup) — bulk DNS lookup: A, AAAA, MX, NS, TXT, SPF and DMARC per domain
- [WHOIS Lookup](https://apify.com/ucchiii/whois-lookup) — bulk WHOIS (RDAP): registrar, created and expiry dates, nameservers, no personal data
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
