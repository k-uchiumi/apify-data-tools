# Domain Availability API: Is a Domain Taken?

**Run it on Apify:** https://apify.com/ucchiii/domain-availability-api · **Price:** $1.50 per 1,000 checked domains · free demo with empty input

**Domain Availability API: send domain names from your code, get back one short JSON row per name — is it taken (`available: false`), free (`available: true`) or undecided (`null`).**

> **Acceptable use:** Do not use this Actor to register domains that contain someone else's trademark or brand (cybersquatting) or for impersonation. Follow the terms of use of the registries' RDAP services.

**For developers and automation builders** who need a yes/no domain check inside an app, a script, a spreadsheet job or a no-code flow (Zapier, Make, n8n): one HTTP call in, a small JSON array out. No API keys of your own, no scraping, no proxies — the answer comes from the registries' own RDAP services and DNS.

- **Short rows by default**: `domain`, `available`, `status`, `tld`, `checkedAt`, `error`. Registrar and dates only when you ask (`details: true`).
- **Up to 1,000 names per run** (call it again for more), cleaned for you: lowercased, URLs cut to the host name, internationalized names converted to punycode, duplicates removed.
- **Pay only for answers**: $0.0015 per name that comes back `registered` or `available`. `unknown` and `invalid` rows are free, and there is no per-run fee, so many tiny calls cost the same as one big one.

## Call it from code

The quickest way is the synchronous endpoint `run-sync-get-dataset-items`: it starts a run, waits for it and returns the rows as the HTTP response. Replace `<YOUR_APIFY_TOKEN>` with your token from Apify Console → Settings → API & Integrations.

### curl
```bash
curl -s -X POST "https://api.apify.com/v2/acts/ucchiii~domain-availability-api/run-sync-get-dataset-items" \
  -H "Authorization: Bearer <YOUR_APIFY_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"domains": ["apify.com", "my-new-startup-name-2026.com", "python.org"]}'
```

### Python
```python
import requests

resp = requests.post(
    "https://api.apify.com/v2/acts/ucchiii~domain-availability-api/run-sync-get-dataset-items",
    headers={"Authorization": "Bearer <YOUR_APIFY_TOKEN>"},
    json={"domains": ["apify.com", "my-new-startup-name-2026.com", "python.org"]},
    timeout=300,
)
for row in resp.json():
    print(row["domain"], row["available"], row["status"], row["error"])
```

### JavaScript (Node.js 18+)
```js
const res = await fetch(
  "https://api.apify.com/v2/acts/ucchiii~domain-availability-api/run-sync-get-dataset-items",
  {
    method: "POST",
    headers: { Authorization: "Bearer <YOUR_APIFY_TOKEN>", "Content-Type": "application/json" },
    body: JSON.stringify({ domains: ["apify.com", "my-new-startup-name-2026.com", "python.org"] }),
  },
);
const rows = await res.json();
const free = rows.filter((r) => r.available === true).map((r) => r.domain);
console.log(free);
```

Keep your token on the server side; do not ship it in a public web page.

### What comes back
```json
[
  {"domain": "python.org", "available": false, "status": "registered", "tld": "org", "checkedAt": "2026-11-13T02:17:04Z", "error": null},
  {"domain": "this-domain-should-not-exist-9f3k2.org", "available": true, "status": "available", "tld": "org", "checkedAt": "2026-11-13T02:17:04Z", "error": null},
  {"domain": "apify.com", "available": false, "status": "registered", "tld": "com", "checkedAt": "2026-11-13T02:17:04Z", "error": null}
]
```
*(Real rows from a run on Apify on 2026-11-13.)* Rows can come back in a different order than you sent them — match them by `domain`.

**Large lists:** a synchronous call has a time limit on Apify's side, so for thousands of names start a normal run (`POST /v2/acts/ucchiii~domain-availability-api/runs`), wait for it to finish, then read `GET /v2/datasets/<defaultDatasetId>/items`. The `apify-client` packages for Python and JavaScript do this for you (`client.actor("ucchiii/domain-availability-api").call(run_input=...)`, then iterate the run's dataset).

**No-code tools:** use the Apify app or an HTTP request step with the same URL and JSON body; map `available` to your yes/no field.

## Use it from an AI agent (MCP)
Apify runs a hosted MCP server, so an AI assistant (Claude, Cursor, VS Code and other MCP clients) can call this Actor as a tool. Load only this Actor by naming it in the server URL:

```json
{
  "mcpServers": {
    "apify": { "url": "https://mcp.apify.com?tools=ucchiii/domain-availability-api" }
  }
}
```

On first use the client opens a browser so you can sign in to Apify (OAuth); a `Authorization: Bearer <APIFY_TOKEN>` header works too. Then ask in plain words, for example "Is mybrand.io free? Check mybrand.com and mybrand.app as well." Each answered name is charged as in a normal run; `unknown` and `invalid` rows stay free. Setup details: [Apify MCP server docs](https://docs.apify.com/platform/integrations/mcp).

## Input
| field | type | default | what it does |
|---|---|---|---|
| `domains` | array of strings | — | Names to check, up to 1,000 per run (start more runs for bigger lists). A single string with names separated by new lines or commas is accepted too. Empty → free demo. |
| `details` | boolean | `false` | Add registrar, IANA ID, created / updated / expiry dates, days to expiry, registry status codes, nameservers and `method` to each row. Same price. |
| `concurrency` | integer | `10` | Names checked in parallel (1–25). |
| `timeoutSecs` | integer | `5` | Per-request timeout (1–60 s). Failed requests are retried up to 3 times. |

## Output
| field | meaning |
|---|---|
| `domain` | the name that was checked, normalized (for invalid input: the entry as you sent it) |
| `available` | `true` = looks free to register, `false` = taken, `null` = could not be decided or invalid input |
| `status` | `registered`, `available`, `unknown` or `invalid` |
| `tld` | the top-level domain, e.g. `com` (`null` for invalid input) |
| `checkedAt` | time of the check (UTC) |
| `error` | why a row is `unknown` or `invalid` (see below); `null` otherwise |

| status | how it is decided | charged |
|---|---|---|
| `registered` | the registry's RDAP has the record, or — for TLDs without RDAP and names the registry does not hold — DNS shows nameservers | yes |
| `available` | the registry's RDAP says "not found" **and** DNS shows no nameservers | yes |
| `unknown` | no answer could be trusted: the TLD has no RDAP and no nameservers, a timeout, a rate limit, a server error, or the shared `.com`/`.net` daily limit | no |
| `invalid` | not a valid domain name (e-mail addresses and IP addresses are refused too) | no |

Common `error` codes: `no_rdap_for_tld`, `rdap_timeout`, `rdap_http_<status>`, `dns_check_failed`, `uk_registry_not_queried_no_ns`, `verisign_daily_limit`, `verisign_refused`, `verisign_counter_unavailable`, `invalid_domain`.

The run's key-value store has a `SUMMARY` record with counts per status, the number of charged names and, if any, how many `.com`/`.net` names were skipped and why.

## This Actor or Domain Availability Checker?
Both come from the same developer and decide availability with the same rules (registry RDAP first, DNS before saying "available"). They differ in who they are shaped for:

| | **Domain Availability API** (this one) | [Domain Availability Checker](https://apify.com/ucchiii/domain-availability-checker) |
|---|---|---|
| Made for | calls from code and automations | people pasting a list in Apify Console |
| Row | 6 short fields; details on request | full row: registrar, dates, status codes, nameservers, method |
| Names per run | up to 1,000 | up to 1,000 |
| Defaults | 10 in parallel, 5 s timeout | 3 in parallel, 15 s timeout |
| Price | $0.0015 per answered name | $0.0005 per answered name |

**Pick this one** when a program reads the result and only needs "taken or not". **Pick the Checker** when you want to read a table yourself with the registrar and expiry dates in every row.

## The shared `.com` / `.net` daily limit
To stay polite to Verisign, the registry for `.com` and `.net`, our Actors share one daily budget of lookups to its RDAP service. This Actor uses the same budget as Domain Availability Checker, so together they never send more than that budget. What it means for you:
- When the day's budget is used up, `.com`/`.net` names come back `unknown` with `error: "verisign_daily_limit"` and are **not charged**. Other TLDs keep working. Try those names again later (the budget resets at 00:00 UTC).
- If Verisign refuses requests, `.com`/`.net` lookups stop for the rest of the day (`verisign_refused`, not charged).
- If the shared counter itself cannot be reached, this Actor does **not** guess: `.com`/`.net` names come back `unknown` with `verisign_counter_unavailable` (not charged) rather than going over the shared budget.

## Pricing
Pay per event: **$0.0015 per domain** with an answer (`registered` or `available`) — **$1.50 per 1,000 domains**. Store discounts apply automatically on higher Apify plans. `unknown`, `invalid` and the free demo are not charged, and there is no start fee.

On the Apify Free plan, the $5 of monthly platform credit covers about **3,300 answered domains** a month. Set a **maximum charge per run** to cap spend; the run stops cleanly when it is reached and the names not yet checked are counted in `SUMMARY` (`notChecked`).

**Free demo:** call it with an empty input (`{}`) and you get three sample rows marked `_demo: true`, not charged.

## Limits
- Up to 1,000 names per run after duplicates are removed (extra names are not checked and are counted in `SUMMARY` as `overLimit`).
- Polite pacing that input cannot change: at most 2 connections to one registry server at a time and at least 0.25 s between requests to it — about 4 lookups per second per registry. A list spread over several TLDs finishes faster than one that is all `.com`.
- **"Available" means the registry has no record and DNS has no nameservers** — not a promise you can buy it. Reserved, premium-priced or blocked names and registry rules can still stop a registration. Check with your registrar before you rely on it.
- TLDs without an RDAP service (many country codes, e.g. `.io`, `.de`) are decided from DNS only: nameservers → `registered`; no nameservers → `unknown`, because DNS alone cannot prove a name is free.
- `.uk` names are never looked up in the `.uk` registry's RDAP (its terms forbid commercial copying); they are decided from DNS only, as above.
- Subdomains are checked at the registered domain (`www.example.com` → `example.com`). Names under multi-part suffixes such as `co.uk` should be given as the registered domain itself.
- Reusing registry data is subject to each registry's terms; that is your responsibility.

## Privacy
Registrant, administrative, technical and billing contacts are never read or returned — no names, e-mails, phone numbers or addresses. With `details: true` you get only the registrar (a company), dates and status codes, and every row carries `registrantRedacted: true`.

## FAQ
**How fast is one call?** A few names usually come back within seconds; most of the time is the registries' own response time. Each registry is paced at about 4 lookups per second, so a list of 1,000 `.com` names takes several minutes — use a normal run for lists like that.

**Is this WHOIS?** It uses RDAP, the protocol ICANN requires registries to run as the successor of WHOIS, plus a DNS check. It does not connect to port-43 WHOIS servers.

**Why is a name `unknown`?** See `error`: the TLD may have no RDAP service, the registry may have timed out or rate-limited the call, or the shared `.com`/`.net` daily limit may be reached. Unknown rows are free; run them again later.

**Can I send the same name twice?** Yes; duplicates (also `Example.com` vs `https://example.com/`) are merged and checked and charged once.

<!-- more-tools:start -->
## More tools from the same developer

- [Domain Name Generator](https://apify.com/ucchiii/domain-name-ideas) — domain name ideas from keywords: combinations, prefixes, suffixes, blends and TLDs, scored (offline, not availability-checked)
- [Domain Age Checker](https://apify.com/ucchiii/domain-age-checker) — how old is a domain: registration date, age in years and days and an age band for up to 1,000 domains, oldest first
- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
- [DNS Lookup](https://apify.com/ucchiii/dns-records-lookup) — bulk DNS lookup: A, AAAA, MX, NS, TXT, SPF and DMARC per domain
- [Domain Expiry Checker](https://apify.com/ucchiii/domain-expiry-checker) — expiry dates of your own and your clients' domains in bulk, soonest first, with an N-days filter
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
