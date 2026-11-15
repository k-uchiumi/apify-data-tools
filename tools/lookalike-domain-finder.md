# Typosquatting Checker: Lookalike Domain Finder

**Run it on Apify:** https://apify.com/ucchiii/lookalike-domain-finder · **Price:** $9.00 per 1,000 lookalike domains · free demo with empty input

**Typosquatting checker for brand protection: find the lookalike domains of your own brand that are registered and live in DNS, such as typosquats, homoglyphs and other TLDs, before they are used for phishing.**

**Who it's for:** brand-protection, security and SOC teams, IT admins and agencies who look after a brand's domains and want to know who registered `exmaple.com`, `examp1e.com` or `exarnple.com` next to their real `example.com`.
Enter your brand domain. The Actor generates the usual lookalike spellings offline and checks each one **over DNS only** (Cloudflare's public DNS-over-HTTPS, 1.1.1.1). You get **one row for each lookalike that exists**, with the IPs, mail servers (MX) and nameservers and a short risk hint. Spellings that don't exist are not listed and cost nothing.

**Try it free: leave the input empty and press Start.** It runs a demo on `example.com`. The demo is not charged, and its rows carry `_demo: true`.

> **Purpose and acceptable use:** This Actor is for **brand protection and defensive monitoring** of brands you own or are authorised to protect. Use it to find typosquats and phishing lookalikes of your brand so that you can watch, block, report or take them down. It only reports names that already exist in DNS. It does not list unregistered spellings and is not meant for picking names to register.

## Sample output
(Made-up placeholder values that show the shape of the rows.)

| brandDomain | domain | technique | riskLevel | hasA | ips | hasMX | mx | nameservers |
|---|---|---|---|---|---|---|---|---|
| example.com | exmple.com | omission | high | true | 198.51.100.7 | true | 10 mx1.mail-host.example | ns1.parking.example |
| example.com | examp1e.com | homoglyph | medium | true | 203.0.113.5 | false | | ns1.host.example |
| example.com | example.net | tld-swap | low | false | | false | | ns1.your-dns.example |

`example.net` is `low` because it uses the same nameservers as `example.com`, so it is probably one of your own defensive registrations.

## Price
**$0.009 per lookalike domain found** ($9.00 per 1,000). A lookalike counts as found when it resolves (A/AAAA) or has NS or MX records. There is no start fee.
Not charged: generated spellings that don't exist, DNS errors, invalid input and the demo. Checking 300 spellings of one brand and finding 12 live lookalikes costs $0.108.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%. To cap your spend, set a maximum charge per run; the Actor stops cleanly when it reaches it.
On the Apify Free plan, the $5 of monthly platform credit covers about 550 lookalikes found per month.

## What it does
- **Generates lookalikes offline**, one change per candidate:
  - **omission**: `exmple.com`
  - **repetition**: `exaample.com`
  - **transposition**: `exmaple.com`
  - **adjacent-key replacement** (QWERTY): `exanple.com`
  - **adjacent-key insertion**: `exanmple.com`
  - **ASCII homoglyphs** (`rn`↔`m`, `0`↔`o`, `1`↔`l`, `vv`↔`w`, `cl`↔`d` …): `examp1e.com`
  - **hyphenation**: `ex-ample.com`
  - **TLD swaps** from a short list you can edit: `example.net`, `example.co.uk`
  - Optional: **bitsquatting** (one flipped bit) and **Unicode / IDN homoglyphs** (Cyrillic `е` for `e` and similar, returned as punycode `xn--…` together with the Unicode form).
- **Checks each candidate over DNS only**: first an A query. If the name doesn't exist (NXDOMAIN) that one question is all it costs. If it exists, AAAA, NS and MX are asked too.
- **Risk hint per row**: `high` means it can receive email (MX), so look for phishing or impersonation. `medium` means it resolves to a server, so look at the website. `low` means nameservers only, or the same nameservers / IP as your brand (probably yours).
- **Polite and robust**: at most 10 checks in parallel, at least 0.05 s between requests, timeouts, and up to 3 retries with backoff (honours `Retry-After`). One failing name never stops the run.
- **Several brands per run**: up to 20 brand domains. Your own brand domains in the list are never reported as lookalikes of each other.

## Input example
```json
{
  "domains": ["yourbrand.com", "yourbrand.co.uk"],
  "maxCandidatesPerDomain": 300,
  "techniques": ["omission", "repetition", "transposition", "replacement", "insertion", "homoglyph", "hyphenation", "tld-swap", "idn-homoglyph"],
  "tlds": ["com", "net", "org", "co", "io", "shop", "co.uk"]
}
```
The techniques are taken in turn, so a small `maxCandidatesPerDomain` still samples every technique.

## Output
One row per lookalike that exists in DNS:
```json
{
  "brandDomain": "example.com",
  "domain": "exmple.com",
  "unicodeDomain": "exmple.com",
  "technique": "omission",
  "hasA": true,
  "ips": ["198.51.100.7", "2001:db8::7"],
  "hasMX": true,
  "mx": ["10 mx1.mail-host.example", "20 mx2.mail-host.example"],
  "nameservers": ["ns1.parking.example"],
  "riskLevel": "high",
  "riskHint": "can receive email (MX records): check for phishing or impersonation",
  "checkedAt": "2026-11-08T09:00:00Z"
}
```
The **SUMMARY** record in the key-value store has the candidates generated and checked, and the found, not-in-DNS and DNS-error counts per brand. It also lists every name whose DNS check failed, so you can run those again (they were free).

## Next steps for each lookalike
- **Who registered it and when?** Run the found domains through [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) for the registrar, the creation and expiry dates and the nameservers (RDAP, no personal data). A name created last week next to your brand deserves a closer look.
- **All DNS records** (TXT, SPF, DMARC, CAA): [DNS Records Lookup](https://apify.com/ucchiii/dns-records-lookup).
- Then report it to the registrar or hosting provider, add it to your email and web blocklists, or start a dispute procedure with your legal team.
- **Schedule** the run weekly in Apify and connect the dataset to email, Slack or Google Sheets to be alerted when new lookalikes appear.

## Use cases
- Find phishing domains that imitate your login or payment pages before customers report them
- Spot lookalikes with mail servers that could be used for invoice fraud or CEO-fraud emails
- Check which defensive registrations your company already owns (same nameservers → `low`)
- Monitor client brands as an agency or MSSP and hand over a weekly list

## Limits (cannot be raised by input)
- 20 brand domains per run. Default 300 and at most 2,000 candidates per brand, and at most 10,000 candidates per run in total
- DNS only: no WHOIS, RDAP or registry lookups. A registered domain that has no DNS records at all (no NS, A or MX) can't be seen and is not reported
- The answers are those of Cloudflare's public resolver (1.1.1.1). A few TLDs answer every name (wildcard DNS), so lookalikes there may all look "found"; check the IPs, which will be identical across those rows
- The risk hint is a heuristic, not a verdict. Always look at the domain before acting
- At most 10 connections to the resolver and at least 0.05 s between requests. Heavy runs may see DNS errors, which are free and listed in SUMMARY

## FAQ
**Does it tell me which spellings are still free to register?** No. On purpose it reports only names that already exist in DNS: it is a monitoring tool for your own brand.
**Why is a lookalike `low` even though it has MX?** It shares nameservers or an IP with your brand domain, which usually means your company registered it.
**Does it return WHOIS data?** No. Use [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) on the found domains.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
- [DNS Lookup](https://apify.com/ucchiii/dns-records-lookup) — bulk DNS lookup: A, AAAA, MX, NS, TXT, SPF and DMARC per domain
- [Domain Availability Checker](https://apify.com/ucchiii/domain-availability-checker) — bulk domain availability check via RDAP: registered or likely available
- [SSL Certificate Expiry Checker](https://apify.com/ucchiii/ssl-certificate-checker) — SSL/TLS certificate expiry dates, issuer and validity for many hosts, soonest first
- [Domain Expiry Checker](https://apify.com/ucchiii/domain-expiry-checker) — expiry dates of your own and your clients' domains in bulk, soonest first, with an N-days filter
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
