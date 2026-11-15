# Parked Domain Checker: Live, Parked or Dead Site

**Run it on Apify:** https://apify.com/ucchiii/parked-domain-checker · **Price:** $0.70 per 1,000 domain verdicts · free demo with empty input

**Parked domain checker: find out which domains in your list are real live websites, parked or for-sale pages, forwards to another domain, or dead — with the reasons behind every verdict.**

| domain | status | forSale | parkingService | signals |
|---|---|---|---|---|
| apify.com | live | false | null | [] |
| python.org | live | false | null | [] |
| example.com | live | false | null | ["tiny_page"] |
| example.invalid | dead | false | null | ["dns:no_such_domain"] |

*(real rows from runs on Apify on 2026-11-12; each row also has the HTTP status, page title, nameservers, addresses and error. A parked page gives `status: "parked"` with signals such as `ns:<parking service>` or `phrase:this domain is for sale`, see the table below)*

**For anyone who has to clean a list of domains or websites:** remove dead and parked sites from a lead or prospect list
before outreach, check backlink or partner domains for SEO, find which domains in a portfolio point nowhere, or spot
domains that are up for sale.

**Try it:** press **Start** with the prefilled example (3 domains). You get one row per domain in a few seconds (well under
1 cent). Clear the list and press Start for a free demo on 3 sample domains (rows carry `_demo: true`, not charged).

## What it checks
For each domain:
1. **DNS** (over HTTPS): A, AAAA and CNAME of the name, NS of its base domain. A name that does not exist, or has no
   address (also not on `www.`), is `dead` right away — no website request is made.
2. **One home page request** with plain HTTP (no browser): `https://` first, then `http://`, then `www.` if the name itself
   does not answer. Redirects are followed one hop at a time, only while they stay on the same domain (http → https, apex → www).
3. **Our parking rules** on what was found:

| Signal | Example in `signals` | Effect |
|---|---|---|
| Nameservers of a known parking service or domain marketplace | `ns:sedoparking.com` | parked |
| CNAME to a parking page | `cname:parkingpage.namecheap.com` | parked |
| Redirect or meta refresh to a domain marketplace or parking service | `redirect_to_marketplace:dan.com` | parked, for sale |
| Script or frame loaded from a parking service | `resource:parkingcrew.net` | parked |
| Page text such as "this domain is for sale", "buy this domain" | `phrase:this domain is for sale` | parked, for sale |
| Page text such as "this domain is parked", "this domain has expired" | `phrase:this domain has expired` | parked |
| Ad-parking or placeholder text such as "related searches", "future home of" | `weak_phrase:related searches` | parked when 2 match, or 1 on a very short page |
| Very short page (under 300 visible characters and under 5 links) | `tiny_page` | supports a weak signal only; never parked on its own |
| Redirect to another domain | `redirect:newbrand.example` | redirect (the target is not requested) |

The lists of parking nameservers, marketplaces and phrases are our own and not complete. That is why every row lists
the `signals` it was judged on — check them before you delete anything.

## Statuses
- `live` — a normal website answers (also `401`/`403`/`429`: the server is up but refuses automated requests; see `signals`).
- `parked` — parking page, for-sale page, registrar placeholder or expired-domain page, parking nameservers, or a forward to a
  domain marketplace. `forSale: true` when the page or the target says the domain is for sale.
- `redirect` — the home page forwards to another domain (`redirectTarget`, `redirectDomain`).
- `dead` — no such domain (`dns_nxdomain`), no address (`no_address_records`), or the site could not be fetched
  (`connection_failed`, `tls_error`, `timeout`, `http_404`, `http_500`, `redirect_loop`, …).
- `unknown` — the DNS lookup itself failed, so nothing can be said.
- `invalid_input` — the entry is not a domain.

## How to use
1. **Domains**: one per line (up to 1,000; URLs are accepted and reduced to the host name; duplicates are removed).
2. Optional: **Timeout**, **Concurrency**, **User-Agent**.
3. Press **Start**. Filter the table by `status`, export it as CSV, JSON or Excel, or read it with the API.

```json
{ "domains": ["apify.com", "example.com", "python.org"] }
```

## Output
```json
{
  "input": "example.com", "domain": "example.com", "status": "live", "decided": true,
  "forSale": false, "parkingService": null,
  "signals": ["tiny_page"],
  "redirectTarget": null, "redirectDomain": null,
  "finalUrl": "https://example.com/", "httpStatus": 200, "pageTitle": "Example Domain", "textLength": 156,
  "nameservers": ["elliott.ns.cloudflare.com", "hera.ns.cloudflare.com"],
  "addresses": ["104.20.23.154", "172.66.147.243", "2606:4700:10::6814:179a", "2606:4700:10::ac42:93f3"], "cname": null,
  "dnsStatus": "ok", "error": null, "checkedAt": "2026-11-12T09:01:38+00:00"
}
```
*(real row from a run on Apify on 2026-11-12. `tiny_page` alone is a weak signal, so the site stays `live`)*
- `signals` — the reasons for the verdict (see the table above). Also `dns:no_such_domain`, `dns:no_address`, `dns:www_only`,
  `fetch:<error>`, `access_restricted:http_403`, `empty_page`.
- `decided` — `true` when the verdict was decided; only those rows are charged.
- `nameservers`, `addresses`, `cname` — the DNS answers used. `finalUrl`, `httpStatus`, `pageTitle`, `textLength` — the home page.
- **SUMMARY** record: domains checked, live, parked, redirect, dead, unknown, invalid input, for sale and charged domains.

## Notes and limits
- **Plain HTTP, no browser.** A parking page drawn only by JavaScript after loading may look like a short page; parking
  nameservers and parking scripts still catch most of them. A site that blocks automated requests shows as `live` with
  `access_restricted`.
- One home page per domain, no crawling. The redirect target on another domain is never requested. Polite by design:
  at most 2 connections per host, at least 0.5 s between requests to the same host, up to 5 redirects, 500 KB read per page,
  timeouts on every request.
- DNS answers come from Cloudflare's public DNS over HTTPS. No registry (RDAP/WHOIS) lookups are made: this Actor does not
  tell you whether a domain is registered or available to register.
- Safe by design: domains that resolve to internal networks (localhost, private ranges, link-local such as 169.254.169.254)
  are never contacted, including redirect targets — reported as `blocked_private_address` (free).
- "Parked" is a judgement from public signals, not a statement by the owner. Only check domains you have a reason to check.

## Pricing
| Event | Free plan | Paid plans (Store discount tiers Bronze / Silver / Gold) |
|---|---|---|
| Domain verdict (live, parked, redirect, or dead by DNS) | $0.70 per 1,000 | $0.63 / $0.56 / $0.49 per 1,000 |

The price on this page ("from $0.49") is the Gold tier price. You pay only for decided verdicts. DNS lookup failures
(`unknown`), sites that could not be fetched (timeouts, connection or HTTP errors), invalid input and the free demo are
**not charged**. On the Apify Free plan, the $5 of monthly platform credit covers about **7,100 domains** a month.

## FAQ
**Why is a dead domain sometimes charged and sometimes not?** When DNS says the name does not exist or has no address,
the answer is final and charged. When DNS is fine but the site does not answer (timeout, refused connection, HTTP 5xx),
the fetch failed — the row says `dead` with the error, and it is free.

**A domain is marked parked but it is a real site.** Look at `signals`. A nameserver match (`ns:…`) means the domain uses a
parking service's DNS; a phrase match quotes the words found. If a rule is wrong for your case, tell us in the Issues tab.

**Does it tell me who owns the domain or when it expires?** No. It reads DNS and the home page only.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Domain Age Checker](https://apify.com/ucchiii/domain-age-checker) — how old is a domain: registration date, age in years and days and an age band for up to 1,000 domains, oldest first
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Redirect Checker](https://apify.com/ucchiii/redirect-checker) — full redirect chain for each URL, hop by hop, with loop detection
- [DNS Lookup](https://apify.com/ucchiii/dns-records-lookup) — bulk DNS lookup: A, AAAA, MX, NS, TXT, SPF and DMARC per domain
- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
