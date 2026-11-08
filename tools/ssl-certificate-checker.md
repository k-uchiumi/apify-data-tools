# SSL Certificate Expiry Checker: Soonest First

**Run it on Apify:** https://apify.com/ucchiii/ssl-certificate-checker · **Price:** $0.50 per 1,000 checked certificates · free demo with empty input

**SSL certificate expiry checker for a list of domains, soonest expiry first**

**Paste your domains, press Start, and get one row per host sorted by expiry date: the certificates that run out first are at the top.** Each row has days to expiry, the expiry date, the issuer, the names the certificate covers, whether the hostname matches and whether the chain is trusted. Expired, self-signed and wrong-host certificates are read too, so you see exactly what is broken instead of a bare "handshake failed".

| host | daysToExpiry | expiresAt | chainValid | hostnameMatches | issuer | verifyError |
|---|---|---|---|---|---|---|
| expired.example.net | -12 | 2026-10-25T23:59:59Z | false | true | Example CA Inc | certificate has expired |
| shop.example.com | 9 | 2026-11-15T12:00:00Z | true | true | Let's Encrypt |  |
| test.example.org | 41 | 2026-12-17T08:30:00Z | false | true | test.example.org | self-signed certificate |
| www.example.com | 203 | 2027-05-28T23:59:59Z | true | false | DigiCert Inc |  |

(Illustrative values that show the shape of the rows; the full row has more fields, see Output.)

**Try it free:** leave the host list empty and press **Start** — five sample hosts are checked (not charged, rows carry `_demo: true`). The prefilled example checks 3 hosts for under 1 cent.

- **Soonest first:** expired certificates first, then the next to expire. Hosts that could not be checked come last.
- **Only what needs attention:** set **Only certificates expiring within (days)** to 30 and get just the certificates that expire in the next 30 days (plus already expired ones and failures).
- **Reads broken certificates:** the certificate is read without trusting it, then checked separately. `chainValid` and `verifyError` tell you whether it is trusted (OpenSSL with the standard root CAs); `hostnameMatches` tells you whether the name fits, the way browsers check it.
- **Direct and polite:** the Actor opens one TLS connection to each host you list. No third-party API, no crawling, no proxies.

## How to use
1. Put your domains in **Domains, hosts or URLs**, one per line: `example.com`, `mail.example.com:993` (any port that speaks TLS directly), `https://www.example.com/page` (only the host is used) or an IP address.
2. Optional: set **Only certificates expiring within (days)**.
3. Press **Start**. Download the results as CSV, Excel or JSON, or schedule the Actor (for example weekly) to get a sorted renewal list every time.

```json
{
  "hosts": ["apify.com", "www.python.org", "mail.example.com:993", "https://shop.example.com/cart"],
  "expiringWithinDays": 30,
  "timeoutSecs": 10,
  "concurrency": 5
}
```

## Output
One row per host:
```json
{
  "host": "shop.example.com",
  "port": 443,
  "daysToExpiry": 9,
  "expiresAt": "2026-11-15T12:00:00Z",
  "expired": false,
  "ok": true,
  "chainValid": true,
  "hostnameMatches": true,
  "selfSigned": false,
  "issuer": "Let's Encrypt",
  "issuerCN": "R11",
  "subjectCN": "shop.example.com",
  "sanNames": ["shop.example.com", "www.shop.example.com"],
  "validFrom": "2026-08-17T12:00:01Z",
  "notYetValid": false,
  "tlsVersion": "TLSv1.3",
  "ip": "203.0.113.10",
  "serialNumber": "4A3F0C2E9B1D",
  "fingerprintSha256": "9F86D081884C7D659A2FEAA0C55AD015A3BF4F1B2B0B822CD15D6C15B0F00A08",
  "verifyError": null,
  "checkedAt": "2026-11-06T09:00:00Z",
  "error": null,
  "errorDetail": null
}
```
- `daysToExpiry` is whole days left: `0` means it expires within 24 hours, negative means it has expired.
- `ok` is `chainValid` and `hostnameMatches` together.
- `fingerprintSha256` changes when the certificate is replaced, handy to spot renewals between scheduled runs.
- When no certificate could be read, `error` says why (free rows): `invalid_host`, `dns_not_found`, `blocked_private_address`, `connection_refused`, `connection_timeout`, `tls_timeout`, `tls_handshake_failed` (with the reason in `errorDetail`, for example when the port does not speak TLS).

The record `SUMMARY` in the key-value store counts certificates read, failures, expired certificates, invalid chains, hostname mismatches and the soonest expiry.

## Limits (cannot be raised by input)
- 1,000 hosts per run (extra hosts are skipped with a note in `SUMMARY`); duplicates are checked once.
- At most 5 hosts at the same time, exactly one TLS connection per host, timeout up to 30 seconds.
- Direct TLS only: ports that need STARTTLS first (SMTP on 25/587, IMAP on 143) are not supported; use the TLS ports (465, 993, 995, 636 ...).
- Internal and private addresses (localhost, 10.x, 192.168.x, cloud metadata) are refused.
- `chainValid` uses only the certificates the server sends plus the standard trusted roots; a server that forgets an intermediate certificate is reported as invalid, as most non-browser clients would see it.
- Revocation (OCSP/CRL) is not checked.

## Pricing
Pay per event: **$0.0005 per checked certificate** ($0.50 per 1,000 certificates). You pay only for certificates that were read and listed in your dataset. Hosts that could not be reached, invalid input, hosts left out by the days filter and the demo are free. Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%. Set a maximum charge per run to cap spend: the Actor checks only as many hosts as that limit allows and tells you so.
On the Apify Free plan, the $5 of monthly platform credit covers about **10,000 certificates** a month — enough to try it on real data.

## FAQ
**Why is a certificate with `chainValid: false` still shown with dates?** The Actor reads the certificate first and checks it second, so you see the expiry and issuer even for expired, self-signed or untrusted certificates. `verifyError` says what failed.

**Does it check the hostname inside `chainValid`?** No. Trust (`chainValid`) and name (`hostnameMatches`) are separate fields, so a valid certificate served on the wrong host shows `chainValid: true, hostnameMatches: false`.

**Can I check a domain's registration expiry too?** Not here: this Actor reads TLS certificates only. For when the domain name itself expires, see the [Domain Expiry Checker](https://apify.com/ucchiii/domain-expiry-checker) from the same developer.

**How do I monitor certificates over time?** Create a task with your hosts and `expiringWithinDays: 30`, and schedule it weekly. Each run lists only what needs renewing soon.

<!-- more-tools:start -->
## More tools from the same developer

- [Domain Expiry Checker](https://apify.com/ucchiii/domain-expiry-checker) — expiry dates of your own and your clients' domains in bulk, soonest first, with an N-days filter
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Website Technology Detector](https://apify.com/ucchiii/website-technology-detector) — tech stack of websites in bulk: CMS, frameworks, analytics and tag IDs, CDN, consent tools
- [DNS Lookup](https://apify.com/ucchiii/dns-records-lookup) — bulk DNS lookup: A, AAAA, MX, NS, TXT, SPF and DMARC per domain
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
