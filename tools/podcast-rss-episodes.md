# Podcast Episodes from RSS: Search & List Episodes

**Run it on Apify:** https://apify.com/ucchiii/podcast-rss-episodes · **Price:** $1.00 per 1,000 episodes · free demo with empty input

**For podcast researchers, PR and outreach teams, media monitors and developers building podcast tools.** Find shows by keyword (Apple Podcasts directory), by Apple Podcasts URL / iTunes ID, or by RSS feed URL, and get **one clean row per episode**: title, publish date, duration, audio URL, season and episode number. **Audio is never downloaded** — you get the link only.

**Try it free: leave input empty and press Start.** The demo is not charged and its rows carry `_demo: true`.

**Price: $0.001 per episode — 1,000 episodes cost $1.** Failed feeds, empty searches and the demo cost nothing.

## Sample output

| podcastTitle | episodeTitle | publishedAt | durationSeconds | season | episode |
|---|---|---|---|---|---|
| Example History Show | The Fall of Rome, Part 2 | 2026-10-20T07:00:00Z | 2710 | 3 | 12 |
| Example History Show | The Fall of Rome, Part 1 | 2026-10-13T07:00:00Z | 2655 | 3 | 11 |
| Tech Talk Weekly | Shipping AI features | 2026-10-18T05:30:00Z | 3602 | null | null |

## What you can do
- **Search**: `searchTerms` runs the iTunes Search API (country selectable) and reads the top shows' feeds
- **Resolve Apple links**: paste `https://podcasts.apple.com/.../id1234567890` or just the ID (iTunes Lookup API)
- **Read feeds directly**: any public podcast RSS URL
- Limits: **max podcasts** (1–50, default 2), **max episodes per podcast** (1–1000, default 5, newest first), **published after** date
- Fields: `podcastTitle`, `podcastAuthor`, `feedUrl`, `appleUrl`, `episodeTitle`, `publishedAt` (ISO 8601 UTC), `durationSeconds`, `audioUrl`, `audioType`, `audioBytes`, `episodeUrl`, `season`, `episode`, `explicit`, `descriptionText` (plain text, first 500 characters)
- Safe by design: hosts that resolve to internal addresses are never contacted (including redirect targets); feeds are limited to 15 MB and 30 s per request, max 3 in parallel with 0.5 s between requests

## Use cases
- Build a list of shows and guests for outreach or sponsorship research
- Monitor new episodes of competitor or industry podcasts on a schedule
- Feed episode titles and descriptions to an LLM or search index
- Import episodes into your own podcast app or catalogue

## Input
```json
{ "searchTerms": ["python programming"], "appleUrlsOrIds": ["https://podcasts.apple.com/us/podcast/planet-money/id290783428"],
  "feedUrls": ["https://feeds.npr.org/510289/podcast.xml"], "country": "US",
  "maxPodcasts": 5, "maxEpisodesPerPodcast": 20, "publishedAfter": "2026-01-01" }
```

## Output
```json
{ "podcastTitle": "Example History Show", "podcastAuthor": "Example Media", "feedUrl": "https://example.com/feed.xml",
  "appleUrl": "https://podcasts.apple.com/us/podcast/example/id123456789", "episodeTitle": "The Fall of Rome, Part 2",
  "publishedAt": "2026-10-20T07:00:00Z", "durationSeconds": 2710, "audioUrl": "https://example.com/ep12.mp3",
  "audioType": "audio/mpeg", "audioBytes": 43360000, "episodeUrl": "https://example.com/episodes/12",
  "season": 3, "episode": 12, "explicit": false, "descriptionText": "Plain-text description..." }
```
A per-podcast summary (status, episodes found, unresolved inputs) is saved as `SUMMARY`. Feed email addresses are not output.
Episodes and shows belong to their publishers; check their terms before republishing. Not affiliated with Apple.

<!-- more-tools:start -->
## More tools from the same developer

- [RSS Feed Reader & Aggregator](https://apify.com/ucchiii/rss-feed-reader) — many RSS / Atom feeds merged into one clean article list
- [Hacker News API](https://apify.com/ucchiii/hacker-news-api) — Hacker News stories, comments and 'Who is hiring' posts via the official APIs
- [RAG Chunker](https://apify.com/ucchiii/rag-chunker) — split scraped pages into chunks for RAG and vector stores
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
