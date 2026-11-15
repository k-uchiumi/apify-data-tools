# Domain Name Generator: Ideas from Keywords

**Run it on Apify:** https://apify.com/ucchiii/domain-name-ideas · **Price:** $0.20 per 1,000 name ideas · free demo with empty input

**Domain name generator: type a few keywords and get a ranked list of domain name ideas — keyword combinations, prefixes and suffixes, simple blends and hyphen variants across .com, .io, .co, .ai and more, best first.**

| domain | score | method | keywordsUsed | length |
|---|---|---|---|---|
| roast.com | 100 | keyword | roast | 5 |
| coffee.com | 100 | keyword | coffee | 6 |
| roasthq.com | 87 | suffix | roast | 7 |
| roaster.com | 85 | brandable | roast | 7 |
| cofast.com | 81 | blend | coffee, roast | 6 |
| coffeeroast.com | 78 | combination | coffee, roast | 11 |
| getcoffee.com | 73 | prefix | coffee | 9 |

*(real ideas for the keywords `coffee` and `roast`; the full row has more fields, see Output fields.)*

> **Ideas only — NOT availability-checked.** This Actor makes no registry, WHOIS, RDAP or DNS lookups at all; it only combines your words. Many ideas (especially the short `.com` ones) will already be registered. Check the names you like in bulk with our **[Domain Availability Checker](https://apify.com/ucchiii/domain-availability-checker)** — every row carries `checkWith: "ucchiii/domain-availability-checker"` and the `domain` column can be pasted straight into it.

**Try it free:** leave **Keywords** empty and press **Start** — you get 25 ideas for `coffee` + `roast` as a demo (not charged, rows carry `_demo: true`).

## What you get
- **Many kinds of domain name ideas** from your keywords:
  - **keyword** — each keyword on its own (`coffee.com`);
  - **combination** — two keywords together, with and without a hyphen (`coffeeroast.com`, `coffee-roast.com`);
  - **prefix** — common startup prefixes: get, try, go, my, the, use, join, hey, meet, hello (`getcoffee.io`, `try-roast.co`);
  - **suffix** — common suffixes: app, hq, hub, lab, labs, ai, io, now, base, kit, box, stack, spot, works, team, pro, ly, ify (`roasthq.com`, `coffee-hub.co`);
  - **brandable** — short endings joined smoothly: ly, ify, io, er, ster, able (`roaster.com`, `roastable.io`);
  - **blend** — simple portmanteaus of two keywords (`cofast.com`);
  - **digits** — 24, 360 or 365 at the end (`roast24.com`), unless you switch digits off.
- **Every TLD you want**: by default com, io, co, net, org, app and ai, or your own list (`co.uk` works too). The order of your list is your preference.
- **Sorted by a simple score (1–100)**: shorter names, names that start with your keyword and names without hyphens or digits score higher; the earlier a TLD is in your list, the higher its ideas score.
- **Filters**: maximum name length, no hyphens, no digits, and a maximum number of results (default 100, up to 1,000).
- **Instant and private**: everything is computed inside the run. Your keywords are not sent anywhere.

## How to use
1. **Keywords**: one per line, up to 10 (for example `coffee`, `roast`, `bean`). Spaces and accents are removed (`Coffee Shop` → `coffeeshop`, `café` → `cafe`).
2. Optional: **TLDs**, **Maximum name length** (default 20), **No hyphens**, **No digits**, **Maximum results** (default 100).
3. Press **Start**, then send the `domain` column to [Domain Availability Checker](https://apify.com/ucchiii/domain-availability-checker) to see which names are still free.

```json
{
  "keywords": ["coffee", "roast"],
  "tlds": ["com", "io", "co"],
  "maxLength": 12,
  "excludeHyphens": true,
  "maxResults": 200
}
```

## Output fields
- `domain` — the full idea, e.g. `roasthq.com`; `label` — the name without the TLD; `tld`; `length` — characters in the label.
- `method` — how it was made: `keyword`, `combination`, `prefix`, `suffix`, `brandable`, `blend` or `digits`; `keywordsUsed` — the keywords in it.
- `score` — 1–100, higher is better (see above). Rows are sorted by score.
- `hasHyphen`, `hasDigits`.
- `availabilityChecked` — always `false`: nothing was looked up. `checkWith` — `ucchiii/domain-availability-checker`, the Actor to check the ideas with.
- **SUMMARY** record: keywords and TLDs used, filters, ideas generated, returned and charged, ideas left out by the maximum results or the maximum charge, and ignored input.

## Limits
- **No availability, WHOIS or trademark check.** A high score does not mean the name is free or safe to use. Check availability with [Domain Availability Checker](https://apify.com/ucchiii/domain-availability-checker) and check trademarks yourself before you buy.
- Up to 10 keywords, 20 TLDs and 1,000 ideas per run. Extra keywords and invalid TLDs are ignored and listed in the SUMMARY record; if no keyword or no TLD can be used, the run stops with a clear message and nothing is charged.
- Only ASCII letters, digits and hyphens are used (no internationalized names).
- Set a **maximum charge per run** to cap spend: the Actor returns the best ideas first and never charges more than that.

## Pricing
Pay per event: **$0.0002 per name idea** — one row (one name with one TLD). That is **$0.20 per 1,000 ideas**, with no start fee. A default run of 100 ideas costs $0.02.
The demo (empty input) is **free**, and runs that stop on invalid input are not charged.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 25,000 ideas a month.

## FAQ
**Are these domains available?** Not necessarily — this Actor never checks. It is a name generator. Run the ideas through [Domain Availability Checker](https://apify.com/ucchiii/domain-availability-checker) to see which ones are registered and which are likely available.

**Why does it not check availability itself?** To keep it instant, cheap and private: generating 1,000 ideas takes a second and sends nothing to any registry. You check only the names you like.

**Is the order of my keywords important?** Combinations are made in both orders (`coffeeroast` and `roastcoffee`). The score does not depend on the order of the keywords, but it does depend on the order of your TLDs.

**Can I get only short .com names?** Yes: set **TLDs** to `com`, **Maximum name length** to for example `10` and switch on **No hyphens** and **No digits**.

<!-- more-tools:start -->
## More tools from the same developer

- [Domain Availability Checker](https://apify.com/ucchiii/domain-availability-checker) — bulk domain availability check via RDAP: registered or likely available
- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
- [Domain Expiry Checker](https://apify.com/ucchiii/domain-expiry-checker) — expiry dates of your own and your clients' domains in bulk, soonest first, with an N-days filter
- [DNS Lookup](https://apify.com/ucchiii/dns-records-lookup) — bulk DNS lookup: A, AAAA, MX, NS, TXT, SPF and DMARC per domain
- [WHOIS Lookup](https://apify.com/ucchiii/whois-lookup) — bulk WHOIS (RDAP): registrar, created and expiry dates, nameservers, no personal data
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
