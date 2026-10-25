# GA4 & GTM Tag Audit: Google Tag Manager Check

**Run it on Apify:** https://apify.com/ucchiii/ga4-gtm-tag-audit · **Price:** $20.00 per 1,000 site auditeds · free demo with empty input

**For marketers, analysts and agencies who need a quick GA4 audit or GTM audit across many sites.** Paste up to 300 public URLs
and get **one row per site**: is **GA4** (gtag.js) installed, is a **Google Tag Manager** container present, are there
**Consent Mode** signals or a consent tool, are there server-side hints, plus a list of **findings with evidence** and a rough
**maturity level** (`none`, `basic`, `standard`, `advanced`) with the reasons listed.
**$20 per 1,000 sites audited — failures are free.** Leave the input empty for a free demo.

## How this differs from Website Technology Detector
| | Website Technology Detector | **GA4 & GTM Tag Audit** (this Actor) |
|---|---|---|
| Purpose | Detects which technologies a site uses (230+: CMS, frameworks, CDN, analytics, pixels ...) | Audits the **GA4 / GTM** setup signals of a site |
| Output | A list of technologies and IDs, no assessment | Detection **plus findings with evidence and a maturity level** |
| GTM / GA4 | IDs only | IDs, where the tag is loaded (head / body), container contents, consent and server-side signals, duplicates, legacy UA |
| Price | $2 per 1,000 sites | $20 per 1,000 sites |

Use the detector when you only need a technology inventory. Use this Actor when you want to know what the GA4 / GTM setup of a
site looks like and where it may need a closer look.

## Use cases
- **GA4 audit of a client or prospect list**: which sites have GA4, GTM, both, or neither in their page source
- **GTM audit before a migration or handover**: container IDs, duplicate containers, loader placement, leftover Universal Analytics tags
- **Consent check at scale**: Consent Mode markers and known consent tools (OneTrust, Cookiebot, CookieYes, Didomi, Usercentrics, iubenda, Osano, Termly, TrustArc)
- **Server-side tagging hints**: server container endpoints on the site's own subdomain, first-party loaders, Stape markers

## What you get (one row per URL)
| Column | Content |
|---|---|
| `url`, `finalUrl`, `status` | the address you gave, where it ended after redirects, the HTTP status |
| `maturityLevel` | `none`, `basic`, `standard` or `advanced` (rules below) |
| `maturityReasons` | the reasons behind the level, as short sentences |
| `ga4Ids`, `gtmIds`, `universalAnalyticsIds` | IDs found in the page source and in the public Google scripts (comma-separated) |
| `consentModeSignals`, `consentTools` | whether Consent Mode markers were seen; known consent tools found |
| `serverSideSignals` | `true` if a server-side or first-party loading signal was found |
| `relatedTags` | Meta Pixel, TikTok Pixel or LINE Tag signals, when seen |
| `findingCount`, `findingsText` | number of findings and all finding sentences joined with ` | ` |
| `findings` | JSON array of `{severity, message, evidence}`; severity is `info` (what was found) or `check` (worth verifying) |
| `limitations`, `checkedAt`, `error` | what a static check cannot see, time of the request, error code if the page could not be read |

Example findings (illustrative):
```json
[
  {"severity": "info", "message": "GA4 ID G-XXXXXXXXXX is loaded with gtag.js in <head>.",
   "evidence": "script src googletagmanager.com/gtag/js?id=G-XXXXXXXXXX in <head>; gtag('config') in <head>"},
  {"severity": "check", "message": "Both gtag.js and GTM are present; if both send hits to the same GA4 property, events may be counted twice. Could not confirm from static HTML.",
   "evidence": "gtag.js IDs: G-XXXXXXXXXX; GTM: GTM-XXXXXXX"},
  {"severity": "check", "message": "No Consent Mode signal and no known consent tool were found in the static HTML; they may be added at runtime.",
   "evidence": "no consent markers or consent-tool scripts in the page source or Google scripts"}
]
```
Addresses without `https://` are accepted and get `https://` added.

## Maturity level: the rules
The level is a coarse signal, not a grade. It is computed from four yes/no facts:

1. **Core**: a GA4 measurement ID (`G-...`) and/or a GTM container (`GTM-...`) was found in the page source or in the public Google container scripts. Legacy Universal Analytics (`UA-...`) alone does not count.
2. **Consent signal**: a Consent Mode marker (`gtag('consent'...)`, `gcd=`, `gcs=`, or a `gcd` reference in the Google script) or a known consent tool script.
3. **Server-side / first-party loading signal**: the container script points to a server endpoint on the site's own subdomain (`server_container_url` or `/g/collect`), a Stape marker, or a tag loader script served from a non-Google address.

| Level | Rule |
|---|---|
| `none` | No core tag found. |
| `basic` | Core tag found, but no consent signal and no server-side signal. |
| `standard` | Core tag found, plus exactly one of: consent signal, server-side signal. |
| `advanced` | Core tag found, plus both a consent signal and a server-side signal. |

The same rules are in `src/detect.py` (`maturity()` and `MATURITY_RULES`) and are covered by tests. A low level can simply mean a
signal is not visible in static HTML (for example a consent tool injected at runtime); read the `findings` and `maturityReasons` before drawing conclusions.

## What it checks
- GA4 measurement IDs, loaded via gtag.js or an inline `gtag('config')` call, and whether they sit in `<head>` or `<body>`
- GTM containers (script snippet, custom-domain loader, IDs hidden in base64 strings), noscript fallback, multiple containers
- GA4 IDs and consent markers inside the public GTM container script and gtag.js
- gtag.js and GTM together on one page (possible double counting), several GA4 IDs, leftover Universal Analytics IDs
- Consent Mode markers and known consent tools, and mismatches between the two
- Server-side tagging hints, and a `dataLayer` without any GTM ID or gtag.js (possible custom loader)
- Related tag signals: Meta Pixel cookie names, TikTok pixel markers, LINE Tag

## How it works and what it cannot see
- **One page request per URL** (redirects followed, up to 5; HTML limited to the first 2 MB). For each GTM or GA4 ID found it also reads the **public Google script** (`googletagmanager.com/gtm.js?id=...`, `gtag/js?id=...`; at most 3 of each per page, each read once per run).
- **This is a static check.** Tags that GTM or another script injects at runtime, consent-dependent behaviour, and what actually reaches your GA4 property are not visible. Findings use words such as "may", "likely" and "could not confirm" for that reason. Individual tags, triggers and variables inside a container are not audited.
- Polite by design: 3 sites in parallel, one connection per host with a 0.5 second gap, 20 second timeout, an honest User-Agent that names this Actor.
- Internal and private addresses (localhost, private ranges, `169.254.169.254`) and non-http(s) schemes are never contacted, including redirect targets; the row gets `blocked_private_address` or `invalid_url`.

## Price
| Event | Price |
|---|---|
| `site` (one URL fetched and audited, HTTP status below 400) | $0.02 |

**Not charged:** timeouts, DNS failures, redirect loops, HTTP 4xx/5xx pages, blocked and invalid URLs, and the empty-input demo. A row is charged only after it has been written to the dataset. If you set a maximum cost per run, the run stops when the limit is reached.

## Notes
Only request addresses you are allowed to access. Some sites block automated requests; those rows show the HTTP status or error. The findings are signals from public page source, intended as a starting point for a closer look in GA4 and GTM, not a replacement for it.

<!-- more-tools:start -->
## More tools from the same developer

- [Website Technology Detector](https://apify.com/ucchiii/website-technology-detector) — tech stack of websites in bulk: CMS, frameworks, analytics and tag IDs, CDN, consent tools
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
