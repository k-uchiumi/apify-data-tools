# ASN Lookup: IP to ASN, AS Name & Country in Bulk

**Run it on Apify:** https://apify.com/ucchiii/ip-asn-lookup · **Price:** $0.50 per 1,000 ip address with asns · free demo with empty input

**ASN Lookup: IP to ASN, AS name and country for lists of IPv4 / IPv6 addresses and domains**

**For anyone who needs to know which network an IP address belongs to, for many addresses at once:** security and fraud teams triaging logs, SOC analysts enriching alerts, network engineers, hosting and CDN researchers, brand-protection and domain investors checking where sites are hosted. Give this Actor up to 10,000 IP addresses or domains and get **one row per IP: AS number (ASN), AS name, country, and the IP range (start, end, CIDR) it belongs to**.
IPs are looked up in an **offline IPtoASN database built into the Actor**: no lookup API, no API key, no rate limits, results in seconds. Domains are first resolved to their A / AAAA addresses over DNS-over-HTTPS (Cloudflare 1.1.1.1).

**Try it free: leave input empty and press Start** (four sample IPs, not charged, rows carry `_demo: true`).

## Sample output
(Values from the fixture used in our tests; real runs show the current data.)

| input | ip | version | asn | asName | country | rangeStart | rangeEnd |
|---|---|---|---|---|---|---|---|
| 1.1.1.1 | 1.1.1.1 | 4 | 13335 | CLOUDFLARENET - Cloudflare, Inc. | US | 1.1.1.0 | 1.1.1.255 |
| 8.8.8.8 | 8.8.8.8 | 4 | 15169 | GOOGLE - Google LLC | US | 8.8.8.0 | 8.8.8.255 |
| example.com | 2001:db8::1 | 6 | 64510 | EXAMPLE-V6 Example IPv6 network | NL | 2001:db8:: | 2001:db8:0:ffff:ffff:ffff:ffff:ffff |
| 192.168.1.10 | 192.168.1.10 | 4 | | | | | |

## Pricing
**$0.50 per 1,000 IPs** ($0.0005 for each IP address found in the database). You pay only for IPs with a result: AS number, AS name, country and range returned. A domain costs the same as the IPs it resolves to that are found (example.com with one IPv4 and one IPv6 address = 2 IPs).
**Free:** invalid input, IPs not found (private, loopback, reserved, not routed), domains that do not exist or have no address, DNS failures, and the demo. Set a maximum charge per run to cap spend; the Actor stops cleanly when it is reached.
On the Apify Free plan, the $5 of monthly platform credit covers about **10,000 IPs** a month — enough to try it on real data.

## What it does
- **IPv4 and IPv6**: `1.1.1.1`, `2001:4860:4860::8888`, `[2001:db8::1]:443`, `1.1.1.1:443`, `https://1.1.1.1/` all work. IPv4-mapped IPv6 (`::ffff:8.8.8.8`) is looked up as IPv4.
- **Domains too**: `example.com` or `https://www.example.com/page` is resolved to its A (and, if you want, AAAA) addresses, and every address gets its own row (up to 10 per domain) with the domain in `input`.
- **AS number, AS name and country** of the range, plus `rangeStart`, `rangeEnd` and the range as CIDR blocks.
- **Clear "not found" rows**: private, loopback, link-local, multicast, reserved or unrouted addresses come back with `status: not_found` and a `note` saying why (free).
- **Offline and fast**: no external lookup API at run time, so no API keys, no third-party rate limits and nothing sent anywhere for IP entries. Only domains need one DNS question per record type.
- **Chains with other Actors**: point `datasetId` at a dataset of IPs or domains, e.g. the `a` list from DNS Records Lookup.

## Input example
```json
{
  "ips": ["1.1.1.1", "8.8.8.8", "2001:4860:4860::8888", "wikipedia.org"],
  "resolveIpv6": true
}
```

## Output
One row per IP address:
```json
{
  "input": "1.1.1.1",
  "ip": "1.1.1.1",
  "version": 4,
  "status": "ok",
  "asn": 13335,
  "asName": "CLOUDFLARENET - Cloudflare, Inc.",
  "country": "US",
  "rangeStart": "1.1.1.0",
  "rangeEnd": "1.1.1.255",
  "cidr": ["1.1.1.0/24"],
  "note": null,
  "source": "IPtoASN (iptoasn.com, ip2asn-combined)",
  "dbDate": "2026-11-01",
  "error": null,
  "checkedAt": "2026-11-08T09:00:00Z"
}
```

| status | meaning | charged |
|---|---|---|
| `ok` | the IP is in an announced range: ASN, AS name, country and range returned | yes |
| `not_found` | the IP is not in any announced range (`note`: private, loopback, reserved, not_routed ...) | no |
| `nxdomain` / `no_address` | the domain does not exist / has no A or AAAA record | no |
| `error` | invalid input (`invalid_input`) or the DNS lookup failed after retries (e.g. `a:dns_http_503`) | no |

The record `SUMMARY` in the key-value store counts each status, the charged IPs and the database date.

## Data source and freshness
The ASN data is the **IPtoASN** database (`ip2asn-combined.tsv`, by iptoasn.com), a public mapping of announced IP ranges to AS numbers. It is downloaded from its official URL each time the Actor is built, and every row carries the database date in `dbDate`. `country` is the country of the AS that announces the range as listed in that database; it is **not** a geolocation of the person or device using the IP.

## Use cases
- Enrich firewall, web server or sign-up logs with ASN and AS name (spot cloud and hosting providers)
- Find which cloud, CDN or hosting company serves a list of websites
- Group IPs by network operator for abuse reports or block lists
- Check where a domain portfolio, or a competitor's sites, are hosted

## Limits (cannot be raised by input)
- 10,000 entries per run, of which at most 1,000 domains; up to 10 addresses per domain
- DNS: up to 10 domains in parallel, at least 0.05 s between requests to the resolver, timeout per request (default 10 s), at most 3 tries
- The database reflects the routing table at its build date (`dbDate`); recently moved ranges may differ
- No city or ISP-level geolocation, no reverse DNS, no WHOIS of the IP owner

## FAQ
**Does it call an IP lookup API?** No. The lookup runs on the database inside the Actor. The only network request at run time is the DNS question for domain entries.
**Why is my IP `not_found`?** It is private, reserved, or not announced on the public internet in the database's snapshot. Not-found IPs are free.
**Can I look up a whole CIDR range?** Not yet: give single IP addresses. CIDR input comes back as `invalid_input` (free).
**Is `country` where the user is?** No. It is the country listed for the AS in IPtoASN, which often differs from where an IP is used.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [DNS Lookup](https://apify.com/ucchiii/dns-records-lookup) — bulk DNS lookup: A, AAAA, MX, NS, TXT, SPF and DMARC per domain
- [Website Technology Detector](https://apify.com/ucchiii/website-technology-detector) — tech stack of websites in bulk: CMS, frameworks, analytics and tag IDs, CDN, consent tools
- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
- [SSL Certificate Expiry Checker](https://apify.com/ucchiii/ssl-certificate-checker) — SSL/TLS certificate expiry dates, issuer and validity for many hosts, soonest first
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
