# Available Domain Finder: Ideas Already Checked

**Run it on Apify:** https://apify.com/ucchiii/available-domain-finder · **Price:** $2.00 per 1,000 available domain names · free demo with empty input

**Available Domain Finder: give it keywords, get back only the domain names that are free to register — name ideas that are already checked.**

> **Acceptable use:** Do not use this Actor to register domains that contain someone else's trademark or brand (cybersquatting) or for impersonation. Follow the terms of use of the registries' RDAP services. A free name is not a cleared name: check trademarks before you buy.

**For founders, makers, agencies and domain hunters** who are tired of the usual loop: brainstorm names, paste them into a checker, find that nearly all of them are taken, repeat. Enter a few keywords and the Actor does the loop for you: it makes name ideas (the keyword itself, combinations, `get`/`try`/`hq`/`hub`/`ly`/`ify` style prefixes and suffixes, blends), checks every idea with the registry's own RDAP service and DNS, and gives you **only the ones that look free**, best ideas first.

- **You see only what you can use**: no list of taken names to wade through. Each row is an available name with its score and how it was made.
- **Pay only for names found**: $0.002 per available name. Checks are free, so the many taken names cost you nothing, and there is no per-run fee.
- **Bounded runs**: at most 200 ideas are checked by default (up to 500 per run), and you can stop after N available names to cap your spend.

## Sample output
```json
[
  {"domain": "roasthq.org", "available": true, "status": "available", "label": "roasthq", "tld": "org", "length": 7,
   "score": 87, "rank": 6, "method": "suffix", "keywordsUsed": ["roast"], "hasHyphen": false, "hasDigits": false,
   "checkMethod": "rdap+dns", "checkedAt": "2026-11-13T02:17:08Z"}
]
```
*(Real row from a run on Apify on 2026-11-13 with the keywords coffee and roast. Whether a given name is free on the day you run it depends on the registries.)*

**Try it free:** clear the keywords and press **Start**. You get a demo for "coffee" + "roast" on `.org`, `.app` and `.dev` (20 checks). Demo rows carry `_demo: true` and are not charged.

## How it works
1. **Ideas**: your keywords (up to 10) are turned into names with our own rules: the keyword, keyword pairs (also hyphenated), common prefixes and suffixes, brandable endings, simple blends and, unless you turn them off, digit endings. Each name gets a simple score (shorter, keyword-first and hyphen-free names rank higher) and is crossed with your TLDs. The best-scoring ideas come first.
2. **Checks**: the top ideas, up to your **maximum checks**, are looked up in the registry's RDAP service (found through the IANA bootstrap) and then in DNS. A name counts as available only when the registry says "not found" **and** DNS shows no nameservers.
3. **Output**: only available names are written to the dataset (and charged). Taken names, undecided names and the ideas you did not check are counted in `SUMMARY` but never listed.

## Input
| field | type | default | what it does |
|---|---|---|---|
| `keywords` | array of strings | — | Words that describe your project (letters a–z and digits only; up to 10). Empty → free demo. |
| `tlds` | array of strings | com, net, org, app, dev | Endings to try (up to 20), in order of preference. |
| `maxChecks` | integer | `200` | Ideas checked per run, best first (1–500). |
| `maxAvailable` | integer | `100` | Stop after this many available names (1–500). Caps your spend. |
| `maxLength` | integer | `20` | Longest name, not counting the TLD (2–63). |
| `excludeHyphens` / `excludeDigits` | boolean | `false` | Leave out names with hyphens or digits. |
| `concurrency` | integer | `5` | Names checked in parallel (1–10). |
| `timeoutSecs` | integer | `8` | Per-request timeout (1–60 s). Failed requests are retried up to 3 times. |

## Output
| field | meaning |
|---|---|
| `domain` | the available name, e.g. `coffeehq.org` |
| `available` / `status` | always `true` / `available` — only free names are output |
| `label`, `tld`, `length` | the name before the TLD, the TLD and the length of the name |
| `score` | simple 1–100 idea score (shorter, keyword-first, no hyphen or digits, earlier TLD = higher) |
| `rank` | position of the idea in the best-first list that was checked |
| `method` | how the name was made: `keyword`, `combination`, `suffix`, `prefix`, `brandable`, `blend` or `digits` |
| `keywordsUsed` | your keywords in the name |
| `hasHyphen`, `hasDigits` | what the name contains |
| `checkMethod`, `checkedAt` | how and when availability was decided |

Rows arrive as names are found, so they are not in score order — sort by `score` or `rank`. The run's key-value store has a `SUMMARY` record with the ideas generated, checked, available, taken and undecided, the number charged, the ideas not checked and, if any, how many `.com`/`.net` lookups were skipped and why.

## Pricing
Pay per event: **$0.002 per available name found** — **$2.00 per 1,000 names**. Store discounts apply automatically on higher Apify plans. Checks of taken or undecided names, the ideas that are not output and the free demo are not charged, and there is no start fee.

Set **maximum checks** and **stop after this many available names** to bound a run, and a **maximum charge per run** in Apify to cap spend; the run stops cleanly when it is reached. Example: checking 200 ideas and finding 12 free names costs $0.024.

## The shared `.com` / `.net` daily limit
To stay polite to Verisign, the registry for `.com` and `.net`, our Actors share one daily budget of lookups to its RDAP service (the same budget as our Domain Availability Checker and Domain Availability API). Because most `.com` ideas turn out to be taken, this Actor can use a lot of it. What it means for you:
- When the day's budget is used up, `.com`/`.net` ideas are **not checked** (counted as undecided, `verisignSkipped` in `SUMMARY`), are not output and are **not charged**. Other TLDs keep working. Try again later; the budget resets at 00:00 UTC.
- If Verisign refuses requests, `.com`/`.net` lookups stop for the rest of the day. If the shared counter cannot be reached, the Actor does **not** guess: it skips `.com`/`.net` rather than going over the shared budget.
- Want results on a busy day? Add other TLDs such as `org`, `app` or `dev`.

## Limits
- At most 500 checks per run, however many ideas could be generated (`SUMMARY` has `ideasNotCheckedOverMaxChecks`). Start another run with other keywords for more.
- Polite pacing that input cannot change: at most 2 connections to one registry server at a time and at least 0.25 s between requests to it — about 4 lookups per second per registry. 200 checks in one TLD take about a minute.
- **"Available" means the registry has no record and DNS has no nameservers** — not a promise you can buy it. Reserved, premium-priced or blocked names and registry rules can still stop a registration, and a name can be taken between the check and your purchase. Check with your registrar.
- TLDs without an RDAP service (many country codes, e.g. `.io`, `.de`) cannot be decided from DNS alone, so they give no results. `.uk` names are never looked up in the `.uk` registry's RDAP (its terms forbid commercial copying).
- Only the Latin letters a–z, digits and hyphens are used; internationalized names are not generated.
- The ideas are generated from your keywords and our word lists; the Actor does not check trademarks or whether a name is a good fit. Reusing registry data is subject to each registry's terms; that is your responsibility.

## Privacy
No personal data is read or returned. Registrant and contact details in RDAP are never read, and the output has no registrar or owner fields at all, only the names you could register.

## FAQ
**Why are there so few results?** Most short, obvious names are taken. Use more keywords, more TLDs, a larger `maxChecks` (up to 500), or allow hyphens and digits. Free checks mean the taken names cost nothing.

**Why only up to 500 checks?** Every check is a request to a registry, and the `.com`/`.net` registry limits automated volume. The cap keeps the Actor a good citizen.

**Is a free name safe to buy?** It is free as far as the registry and DNS show. It can still be premium-priced, reserved, or too close to a trademark. Check with your registrar and a trademark search.

**Do I need a list of names?** No, you only give keywords. If you already have names to check, use Domain Availability API or Domain Availability Checker.

<!-- more-tools:start -->
## More tools from the same developer

- [Domain Name Generator](https://apify.com/ucchiii/domain-name-ideas) — domain name ideas from keywords: combinations, prefixes, suffixes, blends and TLDs, scored (offline, not availability-checked)
- [Domain Availability API](https://apify.com/ucchiii/domain-availability-api) — domain availability API: short JSON rows with available true/false for up to 1,000 names per run
- [Brand Name Domain Checker](https://apify.com/ucchiii/brand-name-domain-checker) — one row per brand name with registered, available or unknown for 25 major TLDs and the count of free ones
- [Domain Due Diligence](https://apify.com/ucchiii/domain-due-diligence) — check domains before you buy: age in years, expiry, registrar, transfer lock and pending delete, A/MX/NS and a live, parked or for-sale verdict
- [Typosquatting Checker](https://apify.com/ucchiii/lookalike-domain-finder) — brand protection: typosquats and phishing lookalikes of your own domain that exist in DNS
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
