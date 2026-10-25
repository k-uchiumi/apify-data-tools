# Hacker News API: Stories, Comments, Who Is Hiring

**Run it on Apify:** https://apify.com/ucchiii/hacker-news-api · **Price:** $1.00 per 1,000 items · free demo with empty input

**For founders, marketers, researchers and recruiters who need Hacker News data in a spreadsheet instead of copying it by hand.**
Search stories by keyword, date and points, read the top/new/best lists, add comments, or turn the monthly "Ask HN: Who is hiring?" thread into one row per job posting. **$1.00 per 1,000 items**, you pay only for rows returned.

- **Official public APIs only**: the Algolia HN Search API (`hn.algolia.com/api/v1`) and the official Hacker News Firebase API (`hacker-news.firebaseio.com/v0`). No HTML scraping, no login, no proxies.
- **One flat row per story**: `id`, `title`, `url`, `domain`, `points`, `numComments`, `createdAt`, `type` (story, ask, show, job, poll), `hnUrl`, plus `by` (the public HN username).
- **Three modes**: `search` (query, story type, date range, minimum points, newest or most relevant first), `list` (top, new, best, ask, show, job lists) and `whoIsHiring`.
- **Optional comments**: add a capped list of comments per story (text, `createdAt`, `parentId`, `points`, `by`). Comments are free.
- **Who is hiring**: top-level postings of the latest (or a chosen) monthly thread with `company` (text before the first `|`), `remote` and `onsite` flags and a `location` guess.
- **Privacy**: e-mail addresses and phone numbers are removed from all text and replaced with `[removed]`.
- **Free demo**: leave the input empty and the Actor returns 3 sample stories without charging (`_demo: true`).

## Input
```json
{
  "mode": "search",
  "query": "apify",
  "storyType": "show",
  "dateFrom": "2026-01-01",
  "minPoints": 10,
  "sortBy": "date",
  "includeComments": true,
  "maxCommentsPerStory": 20,
  "maxItems": 50
}
```
- `mode`: `search`, `list` or `whoIsHiring`.
- Search: `query`, `storyType` (story, ask, show, job), `sortBy` (date or relevance), `dateFrom` / `dateTo` (UTC, `YYYY-MM-DD`), `minPoints`.
- List: `list` (top, new, best, ask, show, job); `query`, `minPoints`, `dateFrom` / `dateTo` filter the fetched stories.
- Who is hiring: `hiringMonth` (`latest`, `2026-10` or `October 2026`), `query` (all words must appear), `remoteOnly`.
- `maxItems`: hard cap, 1 to 1000 (default 10). Algolia serves at most 1000 hits per query; split longer histories by date range.

## Output (one item per story)
```json
{
  "id": 41234567, "title": "Show HN: A tool for web scraping", "url": "https://example.com/tool", "domain": "example.com",
  "points": 154, "numComments": 61, "createdAt": "2026-10-01T15:04:05Z", "type": "show",
  "hnUrl": "https://news.ycombinator.com/item?id=41234567", "by": "someuser", "text": null,
  "comments": [{"id": 41234999, "by": "other", "text": "Nice work. How do you handle ...", "createdAt": "2026-10-01T16:00:00Z", "parentId": 41234567, "points": null}]
}
```
Who-is-hiring rows have `type: "hiring_post"`, `title` = first line of the posting, `text` = the full posting, and:
```json
{"company": "Acme", "remote": true, "onsite": false, "location": "Berlin", "threadId": 45000000, "threadTitle": "Ask HN: Who is hiring? (October 2026)"}
```
- `url` and `domain` are `null` for text posts (Ask HN). `points` of comments is `null` when the API does not give it.
- `company`, `remote`, `onsite` and `location` are simple guesses from the posting header; the full `text` is always included so you can check.
- A run summary (mode, items returned, thread read) is saved to the key-value store as `SUMMARY`.

## Pricing
Pay per event: **$0.001 per story or posting returned** ($1.00 per 1,000). Comments are included free with their story. Filters are applied before charging. The empty-input demo is free. Set a max charge per run in the run options to cap spend.

## Use cases
- Brand and competitor monitoring: every new story that mentions your product or domain
- Trend and launch research: Show HN posts by date range and points
- Daily or weekly digests of the top stories with the first comments
- Hiring-market signals: companies, locations and remote share from the monthly Who is hiring thread

## Notes
- Data comes only from the public Algolia and Firebase Hacker News APIs. Requests are polite: serial or in small batches with a short delay, timeouts and retries with backoff on 429 / 5xx.
- HN usernames appear only as `by` on stories, postings and comments (public pseudonyms). E-mail addresses and phone numbers are stripped on a best-effort basis; unusual obfuscations may slip through.
- A Who-is-hiring month with no thread returns no rows and is not charged.
- Not affiliated with Y Combinator or Hacker News.

<!-- more-tools:start -->
## More tools from the same developer

- [RSS Feed Reader & Aggregator](https://apify.com/ucchiii/rss-feed-reader) — many RSS / Atom feeds merged into one clean article list
- [Job Postings Normalizer](https://apify.com/ucchiii/job-postings-normalizer) — unify fields, salaries and dates from any job scraper and remove duplicates
- [Himalayas Remote Jobs API](https://apify.com/ucchiii/himalayas-jobs-api) — remote job listings from the official Himalayas API, with attribution links
- [Greenhouse Jobs Scraper](https://apify.com/ucchiii/greenhouse-jobs) — open jobs from any Greenhouse job board via the public Job Board API
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
