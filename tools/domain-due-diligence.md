# Domain Due Diligence: Age, Status & Parked Check

**Run it on Apify:** https://apify.com/ucchiii/domain-due-diligence · **Price:** $3.00 per 1,000 registered domain reports · free demo with empty input

**Domain due diligence before you buy: for every domain in your list, one row that says whether it is registered, how old it is, when it expires, who the registrar is, whether it is locked or about to be deleted, what DNS it has today and what its website shows — live, parked, for sale, forwarding or dead — plus a one-line verdict.**

> **Acceptable use:** Check domains you are thinking of buying, bidding on, renewing or taking over. Do not use this Actor to pick names that contain someone else's trademark or brand (cybersquatting) or for impersonation. Follow the terms of use of the registries' RDAP services.

| domain | verdict | ageYears | daysToExpiry | transferLocked | dnsHasMx | website |
|---|---|---|---|---|---|---|
| python.org | in_use | 31.6 | 2326 | true | true | live |
| apify.com | in_use | 17.4 | 3123 | true | true | live |
| example.com | in_use | 31.3 | 272 | true | false | live |

*(real rows from a run on Apify on 2026-11-13; every row also has the summary sentence, dates, registrar, status codes, A/MX/NS lists and the website signals)*

**For anyone about to spend money on a domain:** founders checking the name they want, domain investors screening a list
of aftermarket or expiring names, agencies taking over a client's domain, and SEO teams vetting a domain before they build
on it. Instead of opening a WHOIS page, a DNS tool and the website one by one, paste the list and get the answers side by side.

**Try it:** press **Start** with the prefilled example (3 domains, about 1 cent). Clear the list and press Start for a free
demo on 3 sample domains (rows carry `_demo: true`, not charged).

## What it checks
For each domain, in this order:
1. **Registration (RDAP)** — the registry's own RDAP service: registered or likely available, created date and **age in
   years**, expiry date and days left, registrar and its IANA ID, and the registry **status codes** turned into plain flags:
   `transfer_locked`, `delete_locked`, `update_locked`, `on_hold`, `pending_delete`, `redemption_period`, `pending_transfer`,
   `auto_renew_period`, `add_period`, `expired`.
2. **DNS today** (Cloudflare DNS over HTTPS) — whether the name has an A record, a mail server (MX) and nameservers (NS),
   with the lists.
3. **The home page** — one plain HTTP request (no browser): `live`, `parked` (parking service, registrar placeholder, ad page),
   `for sale` (marketplace forward or "this domain is for sale" text), `redirect` to another domain, or `dead`. The reasons
   are listed in `websiteSignals`.
4. **Verdict and summary** — one code and one sentence that put it together.

Steps 2 and 3 run only for registered domains. A likely-available name stops after step 1.

## Verdicts
| verdict | Meaning |
|---|---|
| `available` | The registry has no record and DNS has no nameservers: likely free to register (not a guarantee — reserved and premium names exist). Free. |
| `pending_delete` | Registered but in redemption or pending delete: it may drop and become available. |
| `for_sale` | Registered; the website or its forward target says the domain is for sale. |
| `parked` | Registered and parked: no real website. |
| `redirect` | Registered; the home page forwards to another domain (`redirectDomain`, not requested). |
| `in_use` | Registered and serving a live website. |
| `no_website` | Registered, but no working website (no address, connection refused, HTTP error, …). |
| `registered` | Registered; the website could not be judged (DNS lookup failed). |
| `unknown` | No registry answer (timeouts, TLD without RDAP and no nameservers, shared .com/.net daily limit). Free. |
| `invalid` | The entry is not a domain. Free. |

## How to use
1. **Domains**: one per line (up to 1,000; URLs are accepted and reduced to the host name; duplicates are removed).
2. Optional: **Concurrency** and **Timeout**.
3. Press **Start**. Sort by `verdict`, `ageYears` or `daysToExpiry`, export as CSV, JSON or Excel, or read the rows with the API.

```json
{ "domains": ["apify.com", "python.org", "example.com"] }
```

## Output
```json
{
 "domain": "apify.com",
 "verdict": "in_use",
 "summary": "Registered and in use: a live website. 17.4 years old, expires in 3123 days, transfer-locked, receives email (MX).",
 "available": false,
 "registrationStatus": "registered",
 "createdAt": "2009-06-02T17:14:10Z",
 "ageYears": 17.4,
 "expiresAt": "2035-06-02T17:14:10Z",
 "daysToExpiry": 3123,
 "registrar": "Amazon Registrar, Inc.",
 "registrarIanaId": "468",
 "statusFlags": [
  "transfer_locked"
 ],
 "statusCodes": [
  "client transfer prohibited"
 ],
 "transferLocked": true,
 "pendingDelete": false,
 "dnsHasA": true,
 "dnsHasMx": true,
 "dnsHasNs": true,
 "aRecords": [
  "3.170.42.100",
  "3.170.42.109",
  "3.170.42.68",
  "3.170.42.76"
 ],
 "mxHosts": [
  "aspmx.l.google.com",
  "alt1.aspmx.l.google.com",
  "alt2.aspmx.l.google.com",
  "aspmx2.googlemail.com",
  "aspmx3.googlemail.com"
 ],
 "nameservers": [
  "ns-1225.awsdns-25.org",
  "ns-1928.awsdns-49.co.uk",
  "ns-449.awsdns-56.com",
  "ns-839.awsdns-40.net"
 ],
 "website": "live",
 "forSale": false,
 "parkingService": null,
 "redirectDomain": null,
 "finalUrl": "https://apify.com/",
 "httpStatus": 200,
 "pageTitle": "Apify: Marketplace of ready-to-run tools for AI",
 "websiteSignals": [],
 "websiteError": null,
 "rdapServer": "rdap.verisign.com",
 "method": "rdap",
 "registrantRedacted": true,
 "error": null,
 "checkedAt": "2026-11-13T06:48:43Z"
}
```
*(real row from a run on Apify on 2026-11-13)*

- **SUMMARY** record: domains checked, count per verdict, charged domains, and how many .com/.net names were skipped
  because of the shared daily limit.

## Pricing
Pay per event: **$0.003 per registered domain** — **$3 per 1,000 registered domains**, with no start fee. Store discounts
apply automatically on higher Apify plans. Likely-available names, `unknown` rows (no registry answer, timeouts, the shared
.com/.net daily limit), invalid input and the free demo are **not charged**. A registered domain whose website could not be
fetched is still charged: the registry data, DNS and the failed fetch are the report.

On the Apify Free plan, the $5 of monthly platform credit covers about **1,600 registered domains** a month. Set a
**maximum charge per run** to cap spend; the run stops cleanly and the domains not yet checked are counted in `SUMMARY`
(`notChecked`).

## Notes and limits
- **No history from web archives.** The age comes from the registry's creation date. A domain that was dropped and
  registered again shows the new date.
- **No prices.** This Actor does not tell you what a registrar or the seller charges.
- **No owner details.** Registrant, admin, technical and abuse contacts are never read or returned; only the registrar
  company name. Every row carries `registrantRedacted: true`.
- **.uk** registration data is not queried: `.uk` names are judged by DNS only (no dates, no status codes).
- **.com and .net** registry lookups share a daily limit across our domain tools. When it is reached, or the shared
  counter cannot be reached, those names come back `unknown` (free) — run them again later.
- One home page per domain, plain HTTP, no crawling. A site that blocks automated requests shows as `in_use` with
  `access_restricted` in the signals. Parking and for-sale detection uses our own list of parking nameservers,
  marketplaces and phrases; check `websiteSignals` before acting on it.
- Safe by design: domains that resolve to internal networks (localhost, private ranges, 169.254.169.254) are never
  contacted, including redirects (`blocked_private_address`).
- Polite by design: at most 2 connections per registry server or website at a time, with a pause between requests,
  timeouts and limited retries. Up to 1,000 domains per run.

## FAQ
**Is "available" a promise I can register it?** No. It means the registry has no record and DNS has no nameservers. Some
names are reserved or sold at premium prices by the registry; your registrar has the final word.

**Why is `transferLocked` true for most domains?** Registrars set the transfer lock by default to prevent theft. It is
normal on a domain in use; when you buy one, the seller asks the registrar to lift it.

**What does `pending_delete` mean for me?** The owner did not renew and the domain is in the deletion cycle. It may become
available in a few days to weeks, unless it is restored.

**How is this different from the Parked Domain Checker or WHOIS Lookup?** Those return one side each (the website, or the
registry data). This Actor joins them with DNS and adds the age, the status flags and a verdict per domain.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Parked Domain Checker](https://apify.com/ucchiii/parked-domain-checker) — sort a list of domains into live, parked or for sale, redirect and dead, with the reasons for each verdict
- [WHOIS Lookup](https://apify.com/ucchiii/whois-lookup) — bulk WHOIS (RDAP): registrar, created and expiry dates, nameservers, no personal data
- [Domain Availability API](https://apify.com/ucchiii/domain-availability-api) — domain availability API: short JSON rows with available true/false for up to 1,000 names per run
- [Domain Expiry Checker](https://apify.com/ucchiii/domain-expiry-checker) — expiry dates of your own and your clients' domains in bulk, soonest first, with an N-days filter
- [DNS Lookup](https://apify.com/ucchiii/dns-records-lookup) — bulk DNS lookup: A, AAAA, MX, NS, TXT, SPF and DMARC per domain
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
