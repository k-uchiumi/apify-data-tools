# Brand Name Domain Checker: One Name, 25 TLDs

**Run it on Apify:** https://apify.com/ucchiii/brand-name-domain-checker · **Price:** $1.50 per 1,000 name-and-TLD checks · free demo with empty input

**Brand Name Domain Checker: type the names you are considering, see which domain endings are free for each one — .com, .io, .ai, .app and 21 more — in one row per name.**

> **Acceptable use:** Do not use this Actor to register domains that contain someone else's trademark or brand (cybersquatting) or for impersonation. Follow the terms of use of the registries' RDAP services.

**For founders, naming agencies and product teams** who are picking a name and need to know, before they fall in love with it, whether `acme.com` is gone and what is left (`acme.io`? `getacme.app`?). Give bare names without a TLD; get one row per name with a status per TLD and the number of free ones. No API keys of your own, no scraping, no proxies — the answers come from the registries' own RDAP services and DNS.

- **One name, many TLDs**: 25 major TLDs by default (`com`, `net`, `org`, `io`, `ai`, `co`, `app`, `dev`, `xyz`, `me`, `info`, `biz`, `tech`, `online`, `site`, `store`, `shop`, `cloud`, `design`, `studio`, `agency`, `club`, `live`, `one`, `pro`), or pick a smaller set (*startup*, *generic*, *country*) or give your own list.
- **One row per name**: `availableCount`, `availableTlds` and a column per TLD (`tld_com`, `tld_io` ...) with `registered`, `available` or `unknown`. Sort by `availableCount` to see which name has the most free endings.
- **Up to 50 names per run**, cleaned for you: lowercased, a TLD you typed by habit (`acme.com`) cut off, internationalized names converted to punycode, duplicates removed.
- **Pay only for answers**: $0.0015 per name and TLD that comes back `registered` or `available` — 25 TLDs for one name cost at most $0.0375. `unknown` and invalid names are free, and there is no per-run fee.

## What comes back
```json
{
  "name": "apify",
  "availableCount": 0,
  "availableTlds": [],
  "registeredCount": 3,
  "unknownCount": 0,
  "checkedAt": "2026-11-13T02:17:07Z",
  "tld_com": "registered",
  "tld_org": "registered",
  "tld_dev": "registered",
  "unknownReasons": {}
}
```
*(Real row from a run on Apify on 2026-11-13 with the TLDs com, org and dev; the default 25 TLDs give one `tld_…` column each.)* Rows can come back in a different order than you sent them — match them by `name`.

## Input
| field | type | default | what it does |
|---|---|---|---|
| `names` | array of strings | — | Bare names, up to 50 per run. A single string with names separated by new lines or commas is accepted too. Empty → free demo. |
| `tldPreset` | `top25`, `startup`, `generic`, `country` | `top25` | Which TLDs to check. |
| `tlds` | array of strings | — | Your own TLD list (up to 50, e.g. `com`, `io`, `co.uk`). Replaces the preset when filled in. |
| `concurrency` | integer | `10` | Lookups in parallel (1–25). |
| `timeoutSecs` | integer | `8` | Per-request timeout (1–60 s). Failed requests are retried up to 3 times. |

| preset | TLDs |
|---|---|
| `top25` (default) | com, net, org, io, ai, co, app, dev, xyz, me, info, biz, tech, online, site, store, shop, cloud, design, studio, agency, club, live, one, pro |
| `startup` | com, io, ai, co, app, dev, xyz, tech, me, cloud |
| `generic` | com, net, org, info, biz, online, site, store, shop, club, live, pro, one |
| `country` | us, uk, de, fr, nl, ca, au, eu, es, it, jp, in, ch, se |

## Output
| field | meaning |
|---|---|
| `name` | the name that was checked, normalized (for invalid input: the entry as you sent it) |
| `availableCount` | how many TLDs look free to register (`null` for invalid input) |
| `availableTlds` | the free TLDs, in the order they were checked |
| `registeredCount`, `unknownCount` | how many TLDs are taken / could not be decided |
| `tld_<tld>` | one column per checked TLD: `registered`, `available` or `unknown` (dots become underscores: `tld_co_uk`) |
| `unknownReasons` | object TLD → error code for each unknown TLD |
| `checkedAt` | time of the check (UTC) |
| `error` | `invalid_name` for an entry that is not a single valid name (free); `null` otherwise |

| status | how it is decided | charged |
|---|---|---|
| `registered` | the registry's RDAP has the record, or — for TLDs without RDAP — DNS shows nameservers | yes |
| `available` | the registry's RDAP says "not found" **and** DNS shows no nameservers | yes |
| `unknown` | no answer could be trusted: the TLD has no RDAP and no nameservers, a timeout, a rate limit, a server error, or the shared `.com`/`.net` daily limit | no |

Common `unknownReasons` codes: `no_rdap_for_tld`, `rdap_timeout`, `rdap_http_<status>`, `dns_check_failed`, `uk_registry_not_queried_no_ns`, `verisign_daily_limit`, `verisign_refused`, `verisign_counter_unavailable`.

The run's key-value store has a `SUMMARY` record with the TLDs checked, counts per status, the number of charged answers and, if any, how many `.com`/`.net` lookups were skipped and why.

## Call it from code
```bash
curl -s -X POST "https://api.apify.com/v2/acts/ucchiii~brand-name-domain-checker/run-sync-get-dataset-items" \
  -H "Authorization: Bearer <YOUR_APIFY_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"names": ["northwind", "quillpad"], "tldPreset": "startup"}'
```
Replace `<YOUR_APIFY_TOKEN>` with your token from Apify Console → Settings → API & Integrations, and keep it on the server side.

## This Actor or Domain Availability Checker?
Same developer, same rules (registry RDAP first, DNS before saying "available"). The difference is the way in:

| | **Brand Name Domain Checker** (this one) | [Domain Availability Checker](https://apify.com/ucchiii/domain-availability-checker) |
|---|---|---|
| You give | names without TLD | full domain names |
| One row is | one name, with a column per TLD and the count of free ones | one domain |
| Good for | comparing name candidates across TLDs | checking a list of exact domains |

## The shared `.com` / `.net` daily limit
To stay polite to Verisign, the registry for `.com` and `.net`, our Actors share one daily budget of lookups to its RDAP service. This Actor uses the same budget as Domain Availability Checker and Domain Availability API, so together they never send more than that budget.
- When the day's budget is used up, the `.com`/`.net` column comes back `unknown` with `verisign_daily_limit` and is **not charged**. All other TLDs keep working. Try again later (the budget resets at 00:00 UTC).
- If Verisign refuses requests, `.com`/`.net` lookups stop for the rest of the day (`verisign_refused`, not charged).
- If the shared counter itself cannot be reached, this Actor does **not** guess: `.com`/`.net` come back `unknown` with `verisign_counter_unavailable` (not charged).

## Pricing
Pay per event: **$0.0015 per name and TLD** with an answer (`registered` or `available`) — $1.50 per 1,000 answers; one name on all 25 default TLDs is at most $0.0375. Store discounts apply automatically on higher Apify plans. `unknown`, invalid names and the free demo are not charged, and there is no start fee.

Set a **maximum charge per run** to cap spend; the run stops cleanly when it is reached and the names not yet checked are counted in `SUMMARY` (`notChecked`).

**Free demo:** call it with an empty input (`{}`) and you get three sample names on four TLDs (`com`, `org`, `io`, `dev`), marked `_demo: true`, not charged.

## Limits
- Up to 50 names and 50 TLDs per run (extra names are not checked and are counted in `SUMMARY` as `overLimit`).
- Polite pacing that input cannot change: at most 2 connections to one registry server at a time and at least 0.25 s between requests to it — about 4 lookups per second per registry.
- **"Available" means the registry has no record and DNS has no nameservers** — not a promise you can buy it. Reserved, premium-priced or blocked names and registry rules can still stop a registration, and a trademark you do not own can stop you using a name. Check with your registrar before you rely on it.
- **TLDs without an RDAP service** (for example `.io`, and many country codes) are decided from DNS only: nameservers → `registered`; no nameservers → `unknown`, because DNS alone cannot prove a name is free. So `.io` can show you which names are taken, but never that one is free.
- `.uk` is never looked up in the `.uk` registry's RDAP (its terms forbid commercial copying); it is decided from DNS only, as above.
- Whether a country TLD has RDAP is looked up in the IANA registry at run time; if a registry has none, you see `no_rdap_for_tld` in `unknownReasons`.
- Reusing registry data is subject to each registry's terms; that is your responsibility.

## Privacy
Registrant, administrative, technical and billing contacts are never read or returned — no names, e-mails, phone numbers or addresses.

## FAQ
**Is this a trademark check?** No. It only says whether a domain name is registered. Search the trademark registers for your market separately.

**Is this WHOIS?** It uses RDAP, the protocol ICANN requires registries to run as the successor of WHOIS, plus a DNS check. It does not connect to port-43 WHOIS servers.

**Why is a TLD `unknown`?** See `unknownReasons`: the TLD may have no RDAP service, the registry may have timed out or rate-limited the call, or the shared `.com`/`.net` daily limit may be reached. Unknown answers are free; run them again later.

**Can I send the same name twice?** Yes; duplicates (also `Acme` vs `acme.com`) are merged and checked and charged once.

<!-- more-tools:start -->
## More tools from the same developer

- [Domain Availability API](https://apify.com/ucchiii/domain-availability-api) — domain availability API: short JSON rows with available true/false for up to 1,000 names per run
- [Available Domain Finder](https://apify.com/ucchiii/available-domain-finder) — available domain names from your keywords: generated ideas checked with RDAP and DNS, only the free-looking names returned
- [Domain Name Generator](https://apify.com/ucchiii/domain-name-ideas) — domain name ideas from keywords: combinations, prefixes, suffixes, blends and TLDs, scored (offline, not availability-checked)
- [Domain Due Diligence](https://apify.com/ucchiii/domain-due-diligence) — check domains before you buy: age in years, expiry, registrar, transfer lock and pending delete, A/MX/NS and a live, parked or for-sale verdict
- [Typosquatting Checker](https://apify.com/ucchiii/lookalike-domain-finder) — brand protection: typosquats and phishing lookalikes of your own domain that exist in DNS
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
