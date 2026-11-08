# Domain Availability Checker: Bulk Registered or Free (RDAP)

**Run it on Apify:** https://apify.com/ucchiii/domain-availability-checker · **Price:** $0.50 per 1,000 checked domains · free demo with empty input

**Which of these domains are registered, and which look free?**

> **Acceptable use:** Do not use this Actor to register domains that contain someone else's trademark or brand (cybersquatting) or for impersonation. Follow the terms of use of the registries' RDAP services.

- **No per-run fee:** you pay only per domain answered, so splitting a list into many small runs costs the same as one big run.
- **Refused or unknown domains are free:** timeouts, rate limits and TLDs the registry does not answer for come back as `unknown` and are not charged.
- **Registered names come with registrar, creation and expiry dates** wherever the registry publishes them over RDAP (all `.com` and `.net` names).

<!-- usage-stats:start -->
*In the last 30 days: 500+ runs and 50,000+ domains checked, 0 failed runs (Apify public run stats and charged results, including our own tests; as of 3 Nov 2026).*
<!-- usage-stats:end -->

**Got a long list of name ideas, expiring domains or competitor domains, and checking them one by one on a registrar site takes forever?**
Paste the list and get **one row per domain: registered or likely available, plus the registrar, creation date and expiry date for registered names.**
It asks the registries directly through **RDAP** (the modern replacement for WHOIS). No scraping, no proxies, no API keys.

**Try it free:** leave the input empty and press **Start** — you get a demo table in seconds, nothing is charged.

| domain | status | available | registrar | createdAt | expiresAt |
|---|---|---|---|---|---|
| example-registered.com | registered | false | Example Registrar, Inc. | 2015-04-02T10:00:00Z | 2027-04-02T10:00:00Z |
| another-registered.org | registered | false | Sample Registry Services | 2008-11-20T00:00:00Z | 2026-11-20T00:00:00Z |
| surely-not-registered-9f3k2.com | available | true | | | |
| something.nordf | unknown | | | | |

*(illustrative sample rows showing the output format; the free demo shows real results)*

- **Who it's for:** founders and naming agencies shortlisting brand names, domain investors and brokers, SEO people vetting expired or competitor domains, IT teams tracking client domains.
- **Bulk:** up to 1,000 domains per run. Messy input is fine: URLs are cut down to the host name, duplicates are removed, typos come back as `invalid` rows.
- **Price:** $0.0005 per domain checked — **$0.50 per 1,000 domains** (100 domains cost 5 cents). Invalid names and `unknown` results are **not charged**.
  Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.


**How each domain is decided (`method`):** every name is looked up in the registry's RDAP first. If the registry has the record, the row is `registered` with registrar and dates (`method: rdap`) — this includes all `.com` and `.net` names. A domain is reported as `available` only when RDAP says "not found" **and** DNS shows no nameservers (`method: rdap+dns`). If RDAP has no record but DNS shows nameservers (e.g. `ox.ac.uk`), or the TLD has no RDAP service (e.g. `.io`, `.de`, `.jp`) and DNS shows nameservers, the row is `registered` with `method: dns`. **Only these `method: dns` rows have no registrar, dates or registry status fields** (the registry does not publish them over RDAP) — use the WHOIS & DNS lookup Actor if you need more. `registered` and `available` are charged the same per domain whichever method decided them; `unknown` and `invalid` are free.

## What it does
- **Registered or likely available**: HTTP 200 from the registry means registered; 404 ("not found") means likely available
- **For registered names**: registrar and its IANA ID, creation, last-changed and expiry dates, days to expiry, `expired` flag, registry status codes
- **`unknown` instead of guessing**: if the TLD has no RDAP service, or the registry times out or rate-limits you, the row says `unknown` with the reason in `error` (and is free) — you can run those again later
- **Polite to the registries**: small concurrency (default 3, max 5), at most 2 connections and 0.25 s spacing per registry server, 429 and server errors retried up to 3 times with a growing pause (honours `Retry-After`)

## Privacy: no personal data
Registrant, administrative, technical and billing contacts are **never read or output** — no names, e-mails, phone numbers or addresses. Only the registrar (a company), dates and status codes are used. Every row carries `registrantRedacted: true`. E-mail addresses typed into the input are rejected as invalid, not looked up.

## Input example
```json
{
  "domains": ["apify.com", "python.org", "this-domain-should-not-exist-9f3k2.com"],
  "concurrency": 3
}
```
**Empty input runs a free demo** on three domains (not charged, rows carry `_demo: true`).

## Next steps
Got your list of registered and available domains? Take the next step with these Actors from the same developer:
- **Full registration details** → [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois): registrar, expiry, days left and nameservers for the registered names.
- **DNS records** → [DNS Records Lookup](https://apify.com/ucchiii/dns-records-lookup): A, MX, TXT, SPF and DMARC — see whether a domain has a website or email set up.
- **Is the site live, and where does it redirect?** → [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker): HTTP status codes and redirect chains.
- **What is the site built with?** → [Website Technology Detector](https://apify.com/ucchiii/website-technology-detector): CMS, frameworks, analytics and CDN.

## Output
One row per domain in the dataset (a table view is included):
```json
{
  "domain": "example.com",
  "status": "registered",
  "available": false,
  "registrar": "Example Registrar, Inc.",
  "registrarIanaId": "9999",
  "createdAt": "2001-01-01T00:00:00Z",
  "updatedAt": "2026-01-01T00:00:00Z",
  "expiresAt": "2027-01-01T00:00:00Z",
  "daysToExpiry": 100,
  "expired": false,
  "domainStatus": ["client transfer prohibited"],
  "rdapServer": "rdap.example-registry.test",
  "rdapDomain": "example.com",
  "registrantRedacted": true,
  "error": null,
  "checkedAt": "2026-10-23T09:00:00Z"
}
```
(Made-up placeholder values that show the shape of a row.) `status` is one of:
| status | meaning | charged |
|---|---|---|
| `registered` | the registry returned registration data (`method: rdap`, with registrar and dates), or — for names the registry's RDAP does not hold or TLDs without RDAP — DNS shows nameservers (`method: dns`, no registrar/dates) | yes |
| `available` | the registry says "not found" and DNS shows no nameservers (`method: rdap+dns`) | yes |
| `unknown` | could not be decided: no RDAP and no NS for the TLD, or a timeout, rate limit, DNS or server error (see `error`) | no |
| `invalid` | not a valid domain name | no |

The record `SUMMARY` in the key-value store counts each status and the number of charged domains.

## Use cases
- Shortlist brand or startup names: which `.com` / `.io` / `.org` names are taken
- Watch domains you want to buy: who is the registrar, how old is the name, when does it expire
- Screen a list of competitor or backlink domains for expired or never-registered names
- Clean a list of domains from another scraper before further processing

## Limits (cannot be raised by input)
- 1,000 domains per run; up to 5 in parallel; at most 2 connections to one registry server at a time, at least 0.25 s between requests to the same server
- Each request has a timeout (default 15 s, max 60 s) and is retried at most 3 times
- **"Available" means "the registry has no record"**, not a guarantee that you can register it: reserved, blocked or premium-priced names and registry rules can still stop a purchase. Check with your registrar before you rely on it
- TLDs without an RDAP service in the IANA bootstrap return `unknown` (many country-code TLDs)
- Registry data can differ slightly between TLDs (some omit the updated date, for example). A domain in a grace period can show a past expiry date (`expired: true`)
- Reusing registry data is subject to each registry's terms; that is your responsibility

## Pricing
Pay per event: **$0.0005 per domain checked** ($0.50 per 1,000). Invalid and unknown results are free. Set a maximum charge per run to cap spend; the Actor stops cleanly when it is reached.
On the Apify Free plan, the $5 of monthly platform credit covers about **10,000 domains** a month — enough to try it on real data.

## FAQ
**Is this the same as WHOIS?** It returns the same kind of registration data, through RDAP, the protocol ICANN requires registries and registrars to run. It does not connect to port-43 WHOIS servers.
**Why no owner name?** By design (see Privacy). The Actor is for registration status and dates, not for finding people.
**Why do `.uk` domains show no registrar or dates?** The `.uk` registry's terms forbid commercial copying of its data, so `.uk` names (including `.co.uk`) are not looked up in its RDAP. They are decided from DNS only: a name with nameservers comes back `registered` (`method: dns`, charged like other DNS results); a name without nameservers comes back `unknown` (`error: uk_registry_not_queried_no_ns`, not charged), because DNS alone cannot prove it is free.
**Why are some `.com`/`.net` names `unknown` with `verisign_daily_limit` or `verisign_refused`?** To stay polite to the `.com`/`.net` registry (Verisign), the Actor keeps a daily limit on its lookups there, shared by all runs, and stops querying it for the rest of the day (UTC) if the registry refuses requests. Those names come back `unknown` and are not charged; other TLDs are checked as usual. Run them again the next day (UTC).
**Can I check subdomains?** Yes. Registration belongs to the registered domain, so for `www.example.com` the Actor checks `example.com` and shows it in `rdapDomain`. (It has no public-suffix list: for names under multi-part suffixes such as `co.uk`, give the registered domain itself.)
**Need DNS records too?** See the domain WHOIS & DNS lookup Actor from the same developer.

<!-- more-tools:start -->
## More tools from the same developer

- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
- [DNS Lookup](https://apify.com/ucchiii/dns-records-lookup) — bulk DNS lookup: A, AAAA, MX, NS, TXT, SPF and DMARC per domain
- [Website Technology Detector](https://apify.com/ucchiii/website-technology-detector) — tech stack of websites in bulk: CMS, frameworks, analytics and tag IDs, CDN, consent tools
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
