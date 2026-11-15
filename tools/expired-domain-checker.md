# Expired Domain Checker: Pending Delete or Free

**Run it on Apify:** https://apify.com/ucchiii/expired-domain-checker · **Price:** $1.50 per 1,000 checked domains · free demo with empty input

**Expired Domain Checker: paste the domains you want for your own brand and see when each one can be registered — active, past its expiry date, in redemption, pending delete or already free, from the registry's own RDAP status codes and expiry dates.**

> **What it is for:** checking a name for your own company or your own brand, and checking a name before you register it — so you know when it can be registered. **Do not use it to acquire names that look like someone else's brand, or to manipulate search rankings.** Do not use it for cybersquatting or impersonation either. It only checks the list you give it (up to 500 names per run). Follow the terms of use of the registries' RDAP services.

**One question, one answer per name: when can it be registered?** Paste the names you are thinking of using for your own business or brand and see, name by name, whether each one is taken, past its expiry date, in redemption, pending delete or already free — without opening a registrar page for every domain. One run gives you one row per domain with its stage, the registry status codes behind that stage, the expiry date and days left. The answer comes from the registries' RDAP services and DNS: no scraping, no proxies, no API keys, no personal data. It does not look at links, traffic or any other metric of the site that used the name.

**Try it free:** leave the input empty and press **Start** — three sample domains are checked as a demo (not charged, rows carry `_demo: true`).

## The stages
| `status` | what it means | how it is decided |
|---|---|---|
| `active` | registered, expiry date in the future, no expiry-related status code | RDAP answers and shows no expiry stage |
| `expired_grace` | the expiry date has passed, or the registry shows `auto renew period` | the owner or the registrar can usually still renew; the name is not on its way out yet |
| `redemption` | the registry shows `redemption period` (or `pending restore`) | deleted by the registrar, the owner may still be able to restore it for a fee |
| `pending_delete` | the registry shows `pending delete` | the last stage before the name is released; it can no longer be restored |
| `available` | the registry has no record **and** DNS shows no nameservers | likely free to register (see Limits) |
| `unknown` | no answer could be trusted | free |
| `invalid` | not a valid domain name (e-mail addresses and IP addresses are refused) | free |

`registryPhase` shows the exact registry status code (or `expiry date in the past`) that put the name in its stage, so you can check the call yourself. `domainStatus` lists every status code RDAP returned.

## Drop dates are estimates, and off by default
Registries delete a name some days after it enters the last stages, but the length of those stages is set by each registry and the exact release time is not published per name. This Actor therefore never prints a drop date as a fact. If a drop date is present it is in `estimatedDropDate`, it is an **ESTIMATE**, and `estimateNote` always says how it was calculated and which public registry document the numbers come from. A date is only calculated for a TLD whose published registry timeline is built into the Actor and only for `redemption` and `pending_delete` names. In this version no TLD has been added yet, so `estimatedDropDate` is `null` on every row; use `daysToExpiry`, `updatedAt` and `registryPhase` to judge timing yourself. Do not rely on any estimate for a time-critical backorder.

## How to use
1. **Domains**: one per line, up to 500 per run (start more runs for bigger lists). URLs are cut down to the host name, letters are lowercased, internationalized names become punycode and duplicates are removed.
2. Optional: **Concurrency** (1–5, default 3) and **Timeout** per request (default 15 s).
3. Run it, open the dataset and sort or filter by `status`. Schedule it daily in Apify to watch a list.

```json
{ "domains": ["apify.com", "my-watched-name.org", "another-name.net"] }
```

## Output
| field | meaning |
|---|---|
| `domain` | the name that was checked, normalized (for invalid input: the entry as you sent it) |
| `status` | `active`, `expired_grace`, `redemption`, `pending_delete`, `available`, `unknown` or `invalid` |
| `available` | `true` = looks free, `false` = registered (any stage), `null` = unknown or invalid |
| `registryPhase` | the registry status code that decided the stage; `null` for active, available, unknown, invalid |
| `expiresAt`, `daysToExpiry` | expiry date from RDAP (UTC) and whole days until it; negative = already past |
| `domainStatus` | all registry status codes, e.g. `client transfer prohibited`, `redemption period` |
| `estimatedDropDate`, `estimateNote` | ESTIMATE only, see above; `null` when there is none |
| `registrar` | registrar company name |
| `createdAt`, `updatedAt` | registration and last-changed dates |
| `tld` | top-level domain, e.g. `com` |
| `checkedAt` | time of the check (UTC) |
| `error` | why a row is `unknown` or `invalid`; `null` otherwise |
| `registrantRedacted` | always `true`: owner and contact details are never read or returned |

Common `error` codes: `no_rdap_for_tld`, `no_registry_status_for_tld`, `rdap_timeout`, `rdap_http_<status>`, `dns_check_failed`, `uk_registry_not_queried_no_ns`, `verisign_daily_limit`, `verisign_refused`, `verisign_counter_unavailable`, `invalid_domain`.

The run's key-value store has a `SUMMARY` record with the number of names per stage, the number charged and, if any, how many `.com`/`.net` names were skipped and why.

## This Actor or Domain Expiry Checker?
| | **Expired Domain Checker** (this one) | [Domain Expiry Checker](https://apify.com/ucchiii/domain-expiry-checker) |
|---|---|---|
| Made for | a name you want for your own brand: when can it be registered? | renewals of your own and your clients' domains |
| Answer | the stage: active, expired_grace, redemption, pending_delete, available | expiry date and days left, soonest first |
| Needs | a list of names | a list of names or a dataset, optional "within N days" filter |

## The shared `.com` / `.net` daily limit
To stay polite to Verisign, the registry for `.com` and `.net`, our domain Actors share one daily budget of lookups to its RDAP service, and this Actor uses that same budget. What it means for you:
- When the day's budget is used up, `.com`/`.net` names come back `unknown` with `error: "verisign_daily_limit"` and are **not charged**. Other TLDs keep working. Try those names again later (the budget resets at 00:00 UTC).
- If Verisign refuses requests, `.com`/`.net` lookups stop for the rest of the day (`verisign_refused`, not charged).
- If the shared counter itself cannot be reached, this Actor does **not** guess: `.com`/`.net` names come back `unknown` with `verisign_counter_unavailable` (not charged).

## Pricing
Pay per event: **$0.0015 per domain** with a definite answer (`active`, `expired_grace`, `redemption`, `pending_delete` or `available`) — **$1.50 per 1,000 domains**. Store discounts apply automatically on higher Apify plans. `unknown`, `invalid` and the free demo are not charged, and there is no start fee. Set a **maximum charge per run** to cap spend; the run stops cleanly when it is reached and the names not yet checked are counted in `SUMMARY` (`notChecked`).

## Limits
- Up to 500 names per run after duplicates are removed (extra names are not checked and are counted in `SUMMARY` as `overLimit`).
- Polite pacing that input cannot change: at most 2 connections to one registry server at a time and at least 0.25 s between requests to it.
- **The stage is what the registry reports at the moment of the check.** Registrars can differ in how long they keep a name before the registry stage starts, and a name can be renewed or restored at any time. `available` means the registry has no record and DNS has no nameservers — not a promise you can register it (reserved, premium-priced or blocked names and registry rules can still stop a registration).
- TLDs without an RDAP service (many country codes, e.g. `.io`, `.de`) cannot be placed on the cycle: registered names come back `unknown` (`no_registry_status_for_tld`), free.
- `.uk` names are never looked up in the `.uk` registry's RDAP (its terms forbid commercial copying), so they come back `unknown`.
- Subdomains are checked at the registered domain (`www.example.com` → `example.com`).
- Reusing registry data is subject to each registry's terms; that is your responsibility.

## Privacy
Registrant, administrative, technical and billing contacts are never read or returned — no names, e-mails, phone numbers or addresses. Rows carry only the registrar (a company), dates and status codes, and every row has `registrantRedacted: true`.

## FAQ
**Is this WHOIS?** It uses RDAP, the protocol ICANN requires registries to run as the successor of WHOIS, plus a DNS check. It does not connect to port-43 WHOIS servers.

**Why is a name `unknown`?** See `error`: the TLD may have no RDAP service, the registry may have timed out or rate-limited the call, or the shared `.com`/`.net` daily limit may be reached. Unknown rows are free; run them again later.

**Does it find expired domains for me?** No. It checks the names you give it. It does not publish lists of expiring or deleted domains.

<!-- more-tools:start -->
## More tools from the same developer

- [Domain Expiry Checker](https://apify.com/ucchiii/domain-expiry-checker) — expiry dates of your own and your clients' domains in bulk, soonest first, with an N-days filter
- [Domain Due Diligence](https://apify.com/ucchiii/domain-due-diligence) — check domains before you buy: age in years, expiry, registrar, transfer lock and pending delete, A/MX/NS and a live, parked or for-sale verdict
- [Domain Availability API](https://apify.com/ucchiii/domain-availability-api) — domain availability API: short JSON rows with available true/false for up to 1,000 names per run
- [WHOIS Lookup](https://apify.com/ucchiii/whois-lookup) — bulk WHOIS (RDAP): registrar, created and expiry dates, nameservers, no personal data
- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
