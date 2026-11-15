# Email DNS Checker: MX, SPF, DKIM & DMARC in Bulk

**Run it on Apify:** https://apify.com/ucchiii/email-dns-checker · **Price:** $0.70 per 1,000 domain checks · free demo with empty input

**Email DNS checker: see at a glance which of your domains have a broken or missing MX, SPF, DMARC or DKIM setup — one row per domain, with a verdict and the exact reasons.**

| domain | verdict | problems | warnings | spfLookups | dmarcEffectivePolicy |
|---|---|---|---|---|---|
| example.com | pass | [] | ["dmarc_no_rua", "dkim_key_revoked:default", "dkim_key_revoked:google", "dkim_key_revoked:selector1", "dkim_key_revoked:selector2", "dkim_key_revoked:k1", "dkim_key_revoked:s1"] | 0 | reject |
| python.org | pass | [] | ["dmarc_policy_none", "dkim_key_under_2048:google:1024"] | 8 | none |
| github.com | pass | [] | ["dkim_key_under_2048:selector1:1024", "dkim_key_under_2048:k1:1024"] | 10 | quarantine |

*(real rows from a run on Apify on 2026-11-13. Each row also has the MX servers, the SPF and DMARC records, the DKIM keys found and the status)*

**For anyone who looks after more than one domain:** agencies and IT teams checking client domains, mail admins
before a migration to a new mail provider, people cleaning up old brand domains that could be spoofed, and anyone who
needs a list of domains that still miss DMARC.

**Try it:** press **Start** with the prefilled example (3 domains). You get one row per domain in a few seconds (well
under 1 cent). Clear the list and press Start for a free demo on 3 sample domains (rows carry `_demo: true`, not charged).

## What it checks
For each domain, only public DNS records are read (DNS over HTTPS, Cloudflare 1.1.1.1). No mail is sent.

| Part | What is read | Problems (→ `needs_attention`) | Warnings (shown, verdict stays `pass`) |
|---|---|---|---|
| **MX** | MX records of the domain | `mx_points_to_ip:<ip>`, `mx_null_mixed_with_servers` | `mx_missing` |
| **SPF** | the `v=spf1` TXT record, then every `include:` and `redirect=` target, recursively | `spf_missing`, `spf_multiple_records`, `spf_too_many_lookups:<n>`, `spf_plus_all`, `spf_include_without_spf:<domain>`, `spf_loop:<domain>`, `spf_syntax:<term>` | `spf_neutral_all` (`?all`), `spf_no_all`, `spf_ptr_mechanism`, `spf_macro_not_checked:<target>` |
| **DMARC** | `_dmarc.<domain>`; for a subdomain without its own record, the parent domains | `dmarc_missing`, `dmarc_multiple_records`, `dmarc_invalid_policy` | `dmarc_policy_none`, `dmarc_no_rua`, `dmarc_pct_below_100:<n>`, `dmarc_invalid_pct` |
| **DKIM** | `<selector>._domainkey.<domain>` for your selectors and a few common ones | `dkim_selector_not_found:<s>` (a selector **you** gave), `dkim_weak_key:<s>:<bits>` (RSA under 1024 bits) | `dkim_not_found_in_checked_selectors`, `dkim_key_revoked:<s>`, `dkim_key_under_2048:<s>:<bits>` |

**The SPF lookup count.** A receiving server may make at most 10 DNS lookups to evaluate one SPF record. Every
`include`, `a`, `mx`, `ptr`, `exists` and `redirect` counts as one, and so do the ones inside each included record.
Above 10 the whole SPF check fails at the receiver (a "permerror"), so mail can land in spam even though each record
looks fine on its own. This Actor walks every include and redirect and gives you the total in `spfLookups`
(`spf_too_many_lookups:13` means 3 too many). Walking stops after 20 included records (`spf_too_many_lookups:21+`).

**Domains that send no mail** should still say so, or anyone can send mail in their name: a null MX (`0 .`), `v=spf1 -all`
and a DMARC record with `p=reject`. A domain with that setup gets `pass`.

## Verdict and status
- `pass` — no problems. Warnings may still be listed.
- `needs_attention` — at least one problem; `problems` lists every one.
- `status`: `checked` (all lookups answered, charged), `nxdomain` (the domain does not exist, free), `error` (a DNS lookup
  failed — the row says which, free), `invalid_input` (free).

## How to use
1. **Domains**: one per line (up to 1,000; URLs are accepted and reduced to the host name). Enter domains, not e-mail
   addresses.
2. Optional: **DKIM selectors** — if you know them (the `s=` value in the `DKIM-Signature` header of a mail you sent),
   enter them so the check is exact. **Also try common selectors** is on by default.
3. Press **Start**. Filter the table by `verdict`, export as CSV, JSON or Excel, or call it from the API.

```json
{ "domains": ["python.org", "github.com", "example.com"], "dkimSelectors": ["s1"] }
```

## Output
```json
{
 "domain": "github.com",
 "status": "checked",
 "verdict": "pass",
 "problems": [],
 "warnings": [
  "dkim_key_under_2048:selector1:1024",
  "dkim_key_under_2048:k1:1024"
 ],
 "hasMx": true,
 "nullMx": false,
 "mx": [
  "0 github-com.mail.protection.outlook.com"
 ],
 "spfRecord": "v=spf1 ip4:192.30.252.0/22 include:spf.protection.outlook.com include:_netblocks.google.com include:_netblocks2.google.com include:mail.zendesk.com include:_spf.salesforce.com include:servers.mcsv.net include:mktomail.com include:sendgrid.net ip4:62.253.227.114 ip4:166.78.69.169 ip4:166.78.69.170 ip4:166.78.71.131 ~all",
 "spfRecordCount": 1,
 "spfAll": "~all",
 "spfLookups": 10,
 "spfLookupLimitExceeded": false,
 "spfIncludes": [
  "spf.protection.outlook.com",
  "_netblocks.google.com",
  "_netblocks2.google.com",
  "mail.zendesk.com",
  "_spf.salesforce.com",
  "servers.mcsv.net",
  "mktomail.com",
  "sendgrid.net"
 ],
 "dmarcRecord": "v=DMARC1; p=quarantine; sp=reject; pct=100; rua=mailto:dmarc@github.com; ruf=mailto:dmarc@github.com; fo=1",
 "dmarcRecordCount": 1,
 "dmarcFoundAt": "github.com",
 "dmarcPolicy": "quarantine",
 "dmarcSubdomainPolicy": "reject",
 "dmarcEffectivePolicy": "quarantine",
 "dmarcPct": 100,
 "dmarcHasRua": true,
 "dmarcRuaDomains": [
  "github.com"
 ],
 "dkimSelectorsChecked": [
  "default",
  "google",
  "selector1",
  "selector2",
  "k1",
  "s1"
 ],
 "dkimSelectorsFound": [
  "google",
  "selector1",
  "k1",
  "s1"
 ],
 "dkim": [
  {
   "selector": "google",
   "keyType": "rsa",
   "keyBits": 2048,
   "revoked": false
  },
  {
   "selector": "selector1",
   "keyType": "rsa",
   "keyBits": 1024,
   "revoked": false
  },
  {
   "selector": "k1",
   "keyType": "rsa",
   "keyBits": 1024,
   "revoked": false
  },
  {
   "selector": "s1",
   "keyType": "rsa",
   "keyBits": 2048,
   "revoked": false
  }
 ],
 "error": null,
 "checkedAt": "2026-11-13T06:49:49Z"
}
```
*(real row from a run on Apify on 2026-11-13)*
- `spfLookups`, `spfLookupLimitExceeded`, `spfAll` — the total lookup count and the `all` term that applies (also through `redirect=`).
- `dmarcFoundAt` — where the DMARC record was found; for `mail.shop.example` without its own record this is `shop.example`,
  and `dmarcEffectivePolicy` is its `sp=` (or `p=`).
- `dmarcRuaDomains` — only the domain part of the report addresses.
- **SUMMARY** record: domains checked, passed, needing attention, not found, failed, invalid input, charged domains and
  the number of DNS questions sent.

## How this differs from DNS Lookup
[DNS Lookup](https://apify.com/ucchiii/dns-records-lookup) gives you the raw records (A, MX, NS, TXT, …) of every domain.
This Actor reads only the mail records, follows the SPF includes, and **judges** them: the lookup count, the policy,
the keys, and a pass / needs attention verdict with reasons.

## Notes and limits
- **DKIM needs the selector.** DNS has no list of a domain's DKIM keys. Without your selector only the common names are
  tried, so `dkim_not_found_in_checked_selectors` is a warning, not proof that DKIM is missing.
- Not checked: whether a given mail actually passes (this needs the mail itself), MTA-STS, BIMI, TLS-RPT, DNSSEC,
  blocklists, reverse DNS of the mail servers, and the authorization record for report addresses on another domain.
- SPF macros (`%{...}`) are counted but not followed. `a` and `mx` terms are counted, their address lookups are not made.
- Polite by design: at most 10 requests to the resolver at a time, at least 0.02 s apart, retries with back-off,
  timeouts on every request. Records used by many domains (a mail provider's include) are asked once per run.
- Only public DNS data is read. Report addresses in DMARC records are reduced to their domain in the extra fields.

## Pricing
| Event | Free plan | Paid plans (Store discount tiers Bronze / Silver / Gold) |
|---|---|---|
| Domain check (all lookups answered) | $0.70 per 1,000 | $0.63 / $0.56 / $0.49 per 1,000 |

The price on this page ("from $0.49") is the Gold tier price. Domains that do not exist, DNS lookup failures, invalid
input and the free demo are **not charged**. On the Apify Free plan, the $5 of monthly platform credit covers about
**7,100 domains** a month.

## FAQ
**My domain sends mail fine but shows `spf_too_many_lookups`.** Many receivers stop counting at 10 and then treat SPF as
failed, so some of your mail is judged without SPF. Remove includes you no longer use, or replace includes with the
`ip4:`/`ip6:` ranges your provider documents.

**Why is `p=none` only a warning?** `p=none` is a valid first step that collects reports without blocking mail. Move to
`quarantine` or `reject` once the reports show your own mail passes.

**Does it check e-mail addresses?** No. It reads the DNS records of domains only, and refuses entries with `@`.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [DNS Lookup](https://apify.com/ucchiii/dns-records-lookup) — bulk DNS lookup: A, AAAA, MX, NS, TXT, SPF and DMARC per domain
- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
- [Typosquatting Checker](https://apify.com/ucchiii/lookalike-domain-finder) — brand protection: typosquats and phishing lookalikes of your own domain that exist in DNS
- [Domain Expiry Checker](https://apify.com/ucchiii/domain-expiry-checker) — expiry dates of your own and your clients' domains in bulk, soonest first, with an N-days filter
- [SSL Certificate Expiry Checker](https://apify.com/ucchiii/ssl-certificate-checker) — SSL/TLS certificate expiry dates, issuer and validity for many hosts, soonest first
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
