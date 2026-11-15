# Domain Age Checker: Bulk Creation Date via RDAP

**Run it on Apify:** https://apify.com/ucchiii/domain-age-checker · **Price:** $1.50 per 1,000 domain age results · free demo with empty input

**Domain Age Checker: find out how old a list of domains is. One row per domain with the registration date, the age in years and days, an age band, plus expiry, last update, registrar and status codes — oldest first.**

> **Acceptable use:** Check domains you are thinking of buying, own, manage or research. Do not use the results to register names that contain someone else's trademark or brand (cybersquatting) or for impersonation. Follow the terms of use of the registries' RDAP services.

**Who it is for:** domain buyers checking a name before an offer or an auction bid, SEO and link-building teams sorting a list of sites by age, security and fraud teams who want to flag very young domains, and anyone cleaning up a portfolio.

- **Age in the form people ask for:** `25 years, 253 days`, plus whole years, extra days, total days, years with one decimal and a band (`under 1 year`, `1-5 years`, `5-10 years`, `10+ years`).
- **From the registry itself:** the date comes from the `registration` event in the registry's RDAP record (RDAP is the successor of WHOIS that ICANN requires registries to run). No scraping of WHOIS web pages, no third-party "history" databases.
- **Up to 1,000 domains per run**, cleaned for you: URLs cut to the host name, lowercased, internationalized names converted, duplicates removed.
- **Pay only for answers:** `unknown` and `invalid` rows are free, and so are registered domains whose registry does not publish a registration date. An empty input runs a free demo.

## How to use it
1. Paste your domains into **Domains**, one per line (or send them as a JSON array through the API).
2. Choose the **Sort order**: oldest first (default), newest first, or as entered.
3. Start the run. The table view shows the domain, registration date, age, band, expiry, last update and registrar; export it as CSV, Excel or JSON.

From code, call the synchronous endpoint and read the rows directly:

```bash
curl -s -X POST "https://api.apify.com/v2/acts/ucchiii~domain-age-checker/run-sync-get-dataset-items" \
  -H "Authorization: Bearer <YOUR_APIFY_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"domains": ["python.org", "wikipedia.org", "my-shop-2026.com"], "sortBy": "oldest"}'
```

## Input
| field | type | default | what it does |
|---|---|---|---|
| `domains` | array of strings | — | Domains to check, up to 1,000 per run. A single string with new lines or commas also works. Subdomains are checked at their registered domain (`blog.example.com` → `example.com`). Empty → free demo. |
| `sortBy` | `oldest` / `newest` / `input` | `oldest` | Row order. Domains without an age always follow the dated ones; invalid entries come last. |
| `concurrency` | integer | `5` | Domains checked in parallel (1–10). Each registry is paced separately (see Limits). |
| `timeoutSecs` | integer | `15` | Per-request timeout (1–60 s). Failed requests are retried up to 3 times. |

## Output
One row per domain. Real rows from a run on Apify on 2026-11-13:

```json
[
  {"domain": "python.org", "status": "registered", "createdAt": "1995-03-27T05:00:00Z",
   "ageYears": 31, "ageExtraDays": 231, "ageText": "31 years, 231 days", "ageDays": 11554, "ageYearsDecimal": 31.6,
   "ageBand": "10+ years", "expiresAt": "2033-03-28T05:00:00Z", "daysToExpiry": 2326,
   "updatedAt": "2026-08-12T05:01:11Z", "registrar": "Gandi SAS", "registrarIanaId": "81",
   "domainStatus": ["client transfer prohibited"], "rdapServer": "rdap.publicinterestregistry.org",
   "rdapDomain": "python.org", "method": "rdap", "registrantRedacted": true, "error": null,
   "checkedAt": "2026-11-13T05:45:41Z"},
  {"domain": "apify.com", "status": "registered", "createdAt": "2009-06-02T17:14:10Z",
   "ageYears": 17, "ageExtraDays": 164, "ageText": "17 years, 164 days", "ageDays": 6373, "ageYearsDecimal": 17.4,
   "ageBand": "10+ years", "expiresAt": "2035-06-02T17:14:10Z", "daysToExpiry": 3123,
   "registrar": "Amazon Registrar, Inc.", "rdapServer": "rdap.verisign.com", "...": "..."}
]
```
*(Real rows; the second is shortened. A name with no registry record and no nameservers comes back as `"status": "not_registered"`.)*

| field | meaning |
|---|---|
| `domain` | the domain that was checked, normalized (for invalid input: the entry as you gave it) |
| `status` | `registered`, `not_registered`, `unknown` or `invalid` (see below) |
| `createdAt` | registration date from RDAP, UTC |
| `ageYears` + `ageExtraDays` | whole years since the registration date, plus the days since the last anniversary |
| `ageText` | the same, written out: `25 years, 253 days` |
| `ageDays` | total days since the registration date |
| `ageYearsDecimal` | years with one decimal, rounded down (so it never shows more years than `ageYears` allows) |
| `ageBand` | `under 1 year`, `1-5 years`, `5-10 years`, `10+ years` |
| `expiresAt`, `daysToExpiry` | expiry date and whole days until it (negative if past) |
| `updatedAt` | the last change to the registration (RDAP `last changed`) |
| `registrar`, `registrarIanaId` | the registrar company and its IANA ID |
| `domainStatus` | EPP status codes, e.g. `client transfer prohibited`, `pending delete` |
| `rdapServer`, `rdapDomain`, `method` | which registry server answered, for which registered domain, and how the status was decided |
| `registrantRedacted` | always `true`: owner contacts are never read or returned |
| `error` | why a row has no age (see below); `null` otherwise |
| `checkedAt` | time of the check (UTC) |

| status | meaning | charged |
|---|---|---|
| `registered` with an age | the registry's RDAP has the record and publishes a registration date | yes |
| `not_registered` | the registry's RDAP says "not found" **and** DNS shows no nameservers | yes |
| `registered` without an age | the domain exists (RDAP record or nameservers in DNS) but no registration date is available: `no_registration_date`, `no_rdap_record`, `uk_registry_not_queried` | no |
| `unknown` | no answer could be trusted: no RDAP for the TLD and no nameservers, a timeout, a server error, or the shared `.com`/`.net` daily limit | no |
| `invalid` | not a domain name (e-mail and IP addresses are refused too) | no |

Common `error` codes: `no_registration_date`, `no_rdap_record`, `no_rdap_for_tld`, `uk_registry_not_queried`, `rdap_timeout`, `rdap_http_<status>`, `dns_check_failed`, `verisign_daily_limit`, `verisign_refused`, `verisign_counter_unavailable`, `invalid_domain`.

The run's key-value store has a `SUMMARY` record: counts per status and per age band, the oldest and the newest domain, how many rows were charged, and (if any) how many `.com`/`.net` domains were skipped and why.

## What "age" means here
- The age is counted from the **registration date of the current registration**, as the registry reports it today. If a name expired, was deleted and was registered again, the clock starts at the new registration. That is usually what matters to a buyer — but it is not the first date the name was ever used. This Actor does not use web archives or third-party history databases.
- A transfer to another registrar does not reset the date; renewals do not change it.
- Some country-code registries do not run RDAP or do not publish a registration date. Those rows show the domain as `registered` without an age and are free.

## This Actor or WHOIS Lookup?
Both read the same RDAP records. [WHOIS Lookup](https://apify.com/ucchiii/whois-lookup) gives you the raw dates, nameservers and DNSSEC flag for each domain. **Domain Age Checker** answers one question — how old is it — with the age already worked out in years and days, a band you can filter on, rows sorted oldest first, and a `not_registered` answer for names that do not exist yet.

## The shared `.com` / `.net` daily limit
To stay polite to Verisign, the registry for `.com` and `.net`, our Actors share one daily budget of lookups to its RDAP service. This Actor uses that same budget. What it means for you:
- When the day's budget is used up, `.com`/`.net` domains come back `unknown` with `error: "verisign_daily_limit"` and are **not charged**. Other TLDs keep working. Run those domains again later (the budget resets at 00:00 UTC).
- If Verisign refuses requests, `.com`/`.net` lookups stop for the rest of the day (`verisign_refused`, not charged).
- If the shared counter cannot be reached, the Actor does **not** guess: `.com`/`.net` domains come back `unknown` with `verisign_counter_unavailable` (not charged).

## Pricing
Pay per event: **$0.0015 per domain** that comes back with an age or as `not_registered` — **$1.50 per 1,000 domains**. Store discounts apply automatically on higher Apify plans. No start fee. `unknown`, `invalid`, registered-without-a-date rows and the free demo are not charged.

Set a **maximum charge per run** to cap spend: the Actor checks only as many domains as the limit can pay for and reports the rest in `SUMMARY` (`notCheckedMaxCharge`).

**Free demo:** run it with an empty input and you get three sample rows marked `_demo: true`, not charged.

## Limits
- Up to 1,000 domains per run after duplicates are removed; the rest are not checked and are counted in `SUMMARY` (`overLimit`). Split bigger lists into several runs.
- Polite pacing that input cannot change: at most 2 connections to one registry server at a time and at least 0.25 s between requests to it — about 4 lookups per second per registry. A list spread over several TLDs finishes faster than one that is all `.com`.
- `.uk` domains are never looked up in the `.uk` registry's RDAP (its terms forbid commercial copying); they are decided from DNS only and have no age.
- Names under multi-part suffixes such as `co.uk` should be given as the registered domain itself.
- Reusing registry data is subject to each registry's terms; that is your responsibility.

## Privacy
Registrant, administrative, technical, billing and abuse contacts are never read or returned — no names, e-mail addresses, phone numbers or postal addresses. Each row carries only dates, the registrar (a company), status codes and the registry server, and `registrantRedacted: true`.

## FAQ
**Is this the same as the "creation date" in WHOIS?** Yes. RDAP calls it the `registration` event; WHOIS output usually labels it `Creation Date`.

**Why does an old, well-known site show a young age?** The name was probably dropped and registered again at some point; the registry only reports the current registration. See "What 'age' means here".

**Why is a domain `unknown`?** Look at `error`: the TLD may have no RDAP service, the registry may have timed out or rate-limited the request, or the shared `.com`/`.net` daily limit may be reached. Unknown rows are free; try them again later.

**Can I filter to young domains only?** Sort by `newest` and stop reading at the band you care about, or filter the exported table on `ageBand` or `ageDays`.

<!-- more-tools:start -->
## More tools from the same developer

- [WHOIS Lookup](https://apify.com/ucchiii/whois-lookup) — bulk WHOIS (RDAP): registrar, created and expiry dates, nameservers, no personal data
- [Parked Domain Checker](https://apify.com/ucchiii/parked-domain-checker) — sort a list of domains into live, parked or for sale, redirect and dead, with the reasons for each verdict
- [Domain Availability API](https://apify.com/ucchiii/domain-availability-api) — domain availability API: short JSON rows with available true/false for up to 1,000 names per run
- [Domain Expiry Checker](https://apify.com/ucchiii/domain-expiry-checker) — expiry dates of your own and your clients' domains in bulk, soonest first, with an N-days filter
- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
