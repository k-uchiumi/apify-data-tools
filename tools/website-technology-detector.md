# Website Technology Detector: Tech Stack, CMS, Tags

**Run it on Apify:** https://apify.com/ucchiii/website-technology-detector · **Price:** $2.00 per 1,000 site analyseds · free demo with empty input

**Paste a list of websites, get a spreadsheet of what each one is built with.** CMS, framework, hosting/CDN, analytics,
tag managers and ad pixels — one flat row per site, ready for Excel or Google Sheets.

**Try it free:** leave the input empty and press **Start** — you get a demo table in seconds, nothing is charged.

| url | technologyNames | gtmIds |
|---|---|---|
| https://apify.com | Amazon CloudFront, Next.js, Intercom, Google Tag Manager | GTM-MNGXGGB |
| https://www.python.org | Fastly, Font Awesome, jQuery, jQuery UI, Modernizr, Varnish, nginx | |

*(real output from a test run on 2026-10-20)*

- **Who it's for:** agencies, sales teams building prospect lists by technology, market researchers, developers.
- **Bulk:** up to 1,000 URLs per run; one request per site, no proxies or browser needed.
- **Tag IDs included:** GTM, GA4, Google Ads and Meta Pixel IDs found in the page source.
- **Price:** $2 per 1,000 sites analysed (100 sites cost 20 cents). Failed, blocked or error pages are **not charged**.
  Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.

## Use cases
- **Market and competitor research**: which sites in a list run Shopify, WordPress, Webflow, Next.js and so on
- **Prospect lists by technology**: filter a CSV of domains by platform, payment provider or chat tool
- **Tag inventory**: collect the GTM container IDs, GA4 measurement IDs, Ads IDs and pixel IDs that appear in the page source of many sites at once
- **Migration and handover notes**: record what a site currently uses (server, CDN, CMS version when the page states it)

## What you get (one row per URL)
| Column | Content |
|---|---|
| `url`, `finalUrl`, `status` | the address you gave, where it ended after redirects, the HTTP status |
| `technologyNames` | comma-separated list, for example `WordPress, Cloudflare, Google Tag Manager` |
| `categories` | comma-separated categories, for example `CMS, CDN, Tag manager` |
| `gtmIds`, `ga4Ids`, `adsIds`, `metaPixelIds` | public IDs found in the page source |
| `otherTrackingTags` | other tag IDs, as `TikTok Pixel: XXXX, Hotjar: 123456` |
| `technologies` | JSON array: `name`, `category`, `version` (when the page states it) and `evidence` |
| `trackingTags` | JSON array of `{name, id}` |
| `checkedAt`, `error` | time of the request and the error code if the page could not be read |

`evidence` says exactly where each technology was seen, for example `script src contains googletagmanager.com/gtm.js?id=GTM-ABC1234`,
`header server: nginx/1.24.0`, `meta generator: WordPress 6.4.2` or `cookie name _fbp`.

## Example
Input:
```json
{ "urls": ["https://example.com", "shop.example.org", "https://www.python.org"], "includeTracking": true }
```
Output row (shortened, illustrative):
```json
{
  "url": "https://www.example.org", "finalUrl": "https://www.example.org/", "status": 200,
  "technologyNames": "Cloudflare, Google Tag Manager, jQuery",
  "categories": "CDN, JavaScript library, Tag manager",
  "gtmIds": "GTM-XXXXXXX", "ga4Ids": "", "adsIds": "", "metaPixelIds": "", "otherTrackingTags": "",
  "technologies": [{"name": "Google Tag Manager", "category": "Tag manager", "version": null,
                    "evidence": "script src contains googletagmanager.com/gtm.js?id=GTM-XXXXXXX"}],
  "checkedAt": "2026-10-20T09:00:00+00:00", "error": null
}
```
Addresses without `https://` are accepted and get `https://` added.

## What it detects (230+ technologies, rules written for this Actor)
- **CMS, site builders and ecommerce**: WordPress, Shopify, Wix, Squarespace, Webflow, Drupal, Joomla, Ghost, HubSpot CMS, Magento, WooCommerce, BigCommerce, PrestaShop, TYPO3, Framer, static site generators and headless CMS
- **JavaScript and CSS**: React, Next.js, Vue.js, Nuxt, Angular, Svelte, jQuery, Alpine.js, htmx, Bootstrap, Tailwind CSS and more
- **Servers, CDN and hosting**: nginx, Apache, Cloudflare, Fastly, Akamai, Amazon CloudFront, Vercel, Netlify, Google Cloud, Azure and more
- **Analytics and tags**: Google Analytics 4, Universal Analytics, Google Tag Manager (including a loader served from a custom domain), Google Ads, Meta Pixel and Conversions API page markers, TikTok Pixel, LinkedIn Insight Tag, Microsoft Clarity, Hotjar, Segment, Mixpanel, Amplitude, Plausible, Matomo and more
- **Consent tools**: OneTrust, Cookiebot, Didomi, Usercentrics, CookieYes, Iubenda and more
- **Chat, support, payments, fonts, maps, video**: Intercom, Zendesk, Drift, HubSpot, Stripe, PayPal, Klarna, Google Fonts, Adobe Fonts, Google Maps and more

Set `includeTracking` to `false` to leave analytics, tag managers, ad pixels and session-replay tools (and the ID columns) out of the result.

## How it works
- **One request per URL** (redirects followed, up to 5). It reads the response headers, the **names** of cookies the server sets (never their values), the HTML (first 2 MB) and the `src` of script tags. **External JavaScript files are not downloaded.**
- Because only the first page is read, technologies that a site loads after the page is rendered in a browser are not seen. Results reflect what a plain HTTP client receives.
- Polite by design: 8 sites in parallel, at most 2 connections per host, 20 second timeout, an honest User-Agent.
- Internal and private addresses (localhost, private ranges, `169.254.169.254`) are never contacted, including redirect targets; the row gets `blocked_private_address`.
- Detection only: the output lists what is present and where it was seen.

## Price
| Event | Price |
|---|---|
| `site` (one URL fetched and analysed, HTTP status below 400) | $0.002 |

**Not charged:** timeouts, DNS failures, redirect loops, HTTP 4xx/5xx pages, blocked and invalid URLs, and the empty-input demo. A row is charged only after it has been written to the dataset. If you set a maximum cost per run, the run stops when the limit is reached.

## Notes
Only request addresses you are allowed to access. Some sites block automated requests; those rows show the HTTP status or error. Versions are reported only when the page, header or script address states them.

<!-- more-tools:start -->
## More tools from the same developer

- [Website Screenshot API](https://apify.com/ucchiii/website-screenshot) — screenshots and PDFs of web pages: full page, mobile, element, no cookie banners
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Domain WHOIS & DNS Lookup](https://apify.com/ucchiii/domain-whois) — bulk WHOIS (RDAP) and DNS lookup: registrar, expiry, days left, A/MX/NS, no personal data
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
