# Favicon Extractor: Bulk Website Icons & Logos

**Run it on Apify:** https://apify.com/ucchiii/favicon-extractor · **Price:** $0.65 per 1,000 site with icons · free demo with empty input

**Favicon extractor: get the favicon, Apple touch icon and web app manifest icons of many websites at once — one row per site with every icon URL, its size and type, and the best icon picked for you.**

| inputUrl | status | bestIconUrl | bestIconSizes | appleTouchIcon | iconCount |
|---|---|---|---|---|---|
| apify.com | found | https://apify.com/icon0.svg?… | any | https://apify.com/apple-icon.png?… | 9 |
| pypi.org | found | https://pypi.org/static/images/favicon.35549fe8.ico | null | null | 1 |

*(real output on 2026-11-12 for 2 of the 3 prefilled sites, URLs shortened; each row also has the full `icons` list)*

**For anyone who needs website icons or logo marks for a list of companies or domains:** show logos next to companies in a CRM,
lead list or dashboard, build link previews and bookmarks, enrich a scraped list of websites, or audit which of your sites are
missing a favicon or an Apple touch icon.

**Try it:** press **Start** with the prefilled example (3 sites). You get one row per site in a few seconds, charged only for the
sites whose icon file was actually downloaded (well under 1 cent). Clear the list and press Start for a free demo on 3 sample sites
(apify.com, python.org and wikipedia.org; rows carry `_demo: true`, not charged).

## What you get
- **Every icon the site declares** on its home page: `<link rel="icon">`, `shortcut icon`, `apple-touch-icon` (and `-precomposed`)
  and Safari's `mask-icon`, with their `sizes` and `type`. Relative links and `<base href>` are resolved to full URLs.
- **Web app manifest icons**: if the page links a manifest (`<link rel="manifest">`), its `icons` are read too (often 192×192 and 512×512 PNGs).
- **`/favicon.ico` fallback**: when the page declares no icon (or cannot be read, e.g. 403), `/favicon.ico` is tried. It is only
  counted when the file really is an image (an HTML error page is not an icon); its sizes are read from the file.
- **The best icon** for each site (`bestIconUrl`, `bestIconSizes`, `bestIconType`): the largest one; PNG wins at equal size, a
  scalable SVG counts as 512 px, the single-colour `mask-icon` is used only when nothing else exists.
- **Checked, not just declared**: the best icon's file is actually downloaded and must be a real image (an HTML error page is not
  an icon). If it is broken, the next best is tried (up to 3 files per site). The row gets the file size (`bestIconBytes`) and
  the pixel size read from the file (`bestIconImageSize`). If none works, the status is `icon_unreachable` and the site is free.
- **Apple touch icon** and **manifest URL** in their own columns.
- **Optional: save the best icon** (`Save best icon` on): the checked file is saved into the run's key-value store (not
  downloaded twice) and the row gets a public link (`storedIconUrl`). No extra charge.
- Accepts **domains or URLs** (`apify.com`, `https://www.python.org/about/`); without a scheme, https:// is used. Up to 1,000 sites per run.

## How to use
1. **Websites**: one domain or URL per line (up to 1,000; duplicates are removed).
2. Optional: **Save best icon**, **Timeout**, **Concurrency**, **User-Agent**.
3. Press **Start**. Export the table as CSV, JSON or Excel, or read it with the API.

```json
{ "urls": ["apify.com", "pypi.org", "https://www.python.org"], "downloadBestIcon": true }
```

## Output
```json
{
  "inputUrl": "apify.com", "finalUrl": "https://apify.com/", "domain": "apify.com", "status": "found",
  "bestIconUrl": "https://apify.com/icon0.svg?…", "bestIconSizes": "any", "bestIconType": "image/svg+xml",
  "bestIconBytes": 774, "bestIconImageSize": null,
  "appleTouchIcon": "https://apify.com/apple-icon.png?…", "manifestUrl": null, "iconCount": 9,
  "icons": [
    {"url": "https://apify.com/favicon.ico?…", "rel": "icon", "sizes": "48x48", "type": "image/x-icon", "source": "html"},
    {"url": "https://apify.com/apple-icon.png?…", "rel": "apple-touch-icon", "sizes": "180x180", "type": "image/png", "source": "html"}
  ],
  "homeStatusCode": 200, "error": null, "checkedAt": "2026-11-12T01:17:17+00:00"
}
```
*(real row from a run on 2026-11-12)*
- `status` — `found` (the best icon file was downloaded and is a real image), `icon_unreachable` (icons are declared but none of
  the best 3 files could be fetched as an image — the declared `icons` are still listed), `not_found`, `error` (the page could
  not be fetched) or `invalid_input`. Only `found` is charged.
- `bestIconBytes` / `bestIconImageSize` — size of the fetched file in bytes, and its pixel size read from the file (ICO, PNG, GIF;
  null for SVG/JPEG).
- `icons[]` — `url`, `rel` (`icon`, `shortcut icon`, `apple-touch-icon`, `apple-touch-icon-precomposed`, `mask-icon` or `manifest`),
  `sizes`, `type` (declared, or guessed from the file extension), `source` (`html`, `manifest` or `favicon.ico`); manifest icons also have `purpose`.
- `error` — e.g. `invalid_url`, `blocked_private_address`, `dns_failure`, `timeout`, `redirect_loop`, `too_many_redirects`, or `http_404` when nothing was found;
  for `icon_unreachable`, why the best declared icon failed (e.g. `http_404`, `not_an_image`, `icon_too_large`).
- With **Save best icon**: `storedIconUrl`, `storedIconKey`, and `storedIconError` when the file could not be saved.
- **SUMMARY** record: sites checked, found, icon unreachable (`iconUnreachable`), not found, errors, invalid input, icons saved and charged sites.

## Notes and limits
- **Plain HTTP, no browser.** Icons added only by JavaScript after the page loads are not seen. Sites that block automated
  requests may return an error; set a custom `userAgent` if needed, and only check sites you are allowed to request.
- Only the page you give, its manifest, `/favicon.ico` (when no icon is declared) and up to 3 icon files are requested — no crawling.
  Polite by design: at most 2 connections per host, at least 0.25 s between requests to the same host, up to 5 redirects, timeouts on every request.
- All declared icon links are listed as the site gives them; only the best ones' files are downloaded to check them (files over
  2 MB are skipped). Inline `data:` icons are skipped.
- Safe by design: addresses that resolve to internal networks (localhost, private ranges, link-local such as 169.254.169.254,
  IPv6 loopback/unique-local) are never contacted, including redirect targets — reported as `blocked_private_address`.
- Icons and logos belong to their owners. This Actor only tells you where a site publishes its icons; check that your use is allowed.

## Pricing
| Event | Free plan | Paid plans (Store discount tiers Bronze / Silver / Gold) |
|---|---|---|
| Site with icon (the best icon file was actually downloaded and is an image) | $0.65 per 1,000 | $0.585 / $0.52 / $0.455 per 1,000 |

The price on this page ("from $0.455") is the Gold tier price. You pay only for sites whose icon file was really fetched.
Sites whose declared icon is broken (`icon_unreachable`: 404, an HTML error page, a blocked or unreachable address), sites with
no icon, invalid input, fetch errors and the free demo are **not charged**. Saving the icon (Save best icon) costs nothing extra.
On the Apify Free plan, the $5 of monthly platform credit covers about **7,600 sites with icons** a month.

## FAQ
**Am I charged when a site declares an icon that does not exist?** No. The best icon's file is downloaded and checked; if it
returns 404, an HTML error page or anything that is not an image, the next best icon is tried (up to 3). If none works, the row
has status `icon_unreachable`, still lists the declared icons, and is free.

**Does Save best icon download the file again?** No. The file that was checked is the one saved, so turning it on adds no requests and no charge.

**Why is `bestIconUrl` not the largest declared icon?** The largest one was broken, so the next best working file was used.
All declared icons are still in `icons`.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [Website Technology Detector](https://apify.com/ucchiii/website-technology-detector) — tech stack of websites in bulk: CMS, frameworks, analytics and tag IDs, CDN, consent tools
- [Link Preview API](https://apify.com/ucchiii/link-preview-api) — link previews for URL lists: Open Graph and Twitter Card title, description, image, site name and favicon
- [Website Screenshot](https://apify.com/ucchiii/website-screenshot) — screenshots and PDFs of web pages: full page, mobile, element, no cookie banners
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
