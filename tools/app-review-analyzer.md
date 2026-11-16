# App Review Analyzer: Ratings by Version & Terms

**Run it on Apify:** https://apify.com/ucchiii/app-review-analyzer · **Price:** $5.00 per 1,000 app summaries · free demo with empty input

**App review analyzer: one summary row per app, with ratings by version and complaint terms**

| appName | reviews | avgRating | stars 1/2/3/4/5 | worst version (avg) | top terms in 1-2 star reviews | developerReplyRate |
|---|---|---|---|---|---|---|
| Sample Notes (fictional) | 10 | 3.4 | 2 / 1 / 1 / 3 / 3 | 2.1.0 (2.2) | sync, crash, failed, lost note | 0.5 |
| Example Fit Tracker (fictional) | 8 | 3.12 | 2 / 1 / 1 / 2 / 2 | 5.4 (2.5) | expensive, subscription, battery drain | 0.375 |

*(real output of the free demo: 18 fictional reviews of 2 sample apps; each row also has the monthly averages and the full list of versions)*

**For app owners, product managers and mobile growth analysts who already have their App Store or Google Play reviews in a table.**
Exports and review datasets are long lists nobody has time to read. This Actor turns them into **one summary row per app**: did the rating drop
after version 2.1.0, which month was the worst, what do unhappy users keep writing about, and how often does the developer answer.
No scraping, no LLM, no proxies; the result is rule-based and reproducible.

You bring the reviews. This Actor does not collect anything from the App Store or Google Play. Use one of:
- **Reviews datasets**: pick a dataset from your Apify account (the field lists them; no ID to copy)
- **File URLs**: a link to a JSON, NDJSON or CSV file (up to 50 MB)
- **Paste rows**: a short JSON array in the input

**Try it:** press **Start** with the input as it is (nothing picked). You get the free demo above in a few seconds: 2 app rows built from 18
fictional reviews, plus `STATS`. Not charged. The apps and reviews are fictional; the demo shows the output format.

**Price:** $0.005 per app summary row ($5.00 per 1,000 apps); reading reviews is free and there is no start fee. Paid plans get Store discount tiers.

For each app you get:
- **Rating**: review count, average rating, star distribution (1 to 5), share of 1 to 2 star reviews
- **Monthly average**: review count and average rating per month (`monthly`)
- **Average by app version**: review count and average rating per version, newest first, up to 50 versions (`byVersion`)
- **Frequent terms in 1-2 star reviews**: single words and two-word phrases with counts (`topLowStarTerms`), English stop words removed (add your own)
- **Developer reply rate**: share of reviews that have a developer reply

**Privacy by design:** reviewer names, IDs, avatars and profile links are never read or written, and neither is the review text. Only aggregates
(counts, averages and frequent terms that appear in at least 2 reviews) are in the output.

## Columns are detected automatically
The Actor recognizes common column names, case-insensitively and ignoring spaces and underscores, for example:

| Field | Recognized names (examples) |
|---|---|
| app | `appId`, `bundleId`, `packageName`, `appName`, `app` |
| rating | `rating`, `score`, `stars`, `starRating` |
| date | `date`, `at`, `updated`, `publishedAt`, `reviewDate`, `createdAt`, UNIX seconds or milliseconds |
| version | `version`, `appVersion`, `reviewCreatedVersion` |
| text | `text`, `content`, `review`, `body`, `comment` |
| developer reply | `developerResponse`, `replyContent`, `reply`, `response` |

If it picks the wrong column, set **Column mapping**, e.g. `{"rating": "stars_given", "version": "build"}`. The columns used are saved in `STATS`.
Rows without an app column are grouped into one app named `(all reviews)`; with several apps in one file, include an app ID or name column.

## Input
```json
{
  "datasetIds": ["<dataset ID of your reviews>"],
  "columnMapping": {"version": "build"},
  "topTerms": 20,
  "stopwords": ["mybrand"]
}
```
**Few reviews per app?** With fewer than 20 reviews the monthly, per-version and term results are only indicative; such rows have `lowSample: true`.

## Output (one row per app)
```json
{
  "appId": "com.example.demo.notes", "appName": "Sample Notes (fictional)",
  "reviewsCount": 10, "ratedReviewsCount": 10, "lowSample": true, "avgRating": 3.4,
  "starsDistribution": {"1": 2, "2": 1, "3": 1, "4": 3, "5": 3}, "lowStarShare": 0.3,
  "monthly": [{"period": "2026-09", "count": 4, "avgRating": 1.75}],
  "byVersion": [{"version": "2.1.0", "count": 5, "avgRating": 2.2}],
  "topLowStarTerms": [{"term": "sync", "count": 3}, {"term": "lost note", "count": 2}],
  "developerReplyRate": 0.5, "firstReviewAt": "2026-07-03T10:00:00+00:00", "latestReviewAt": "2026-10-20T10:00:00+00:00"
}
```
Run statistics (`reviewsRead`, `reviewsSkipped`, `apps`, `written`, detected `columns`, `errors`) are saved in the key-value store as `STATS`.

## Notes
- Works on data you already own; it does not access any app store. You are responsible for how the data was collected and used.
- Term analysis is simple (lowercase, basic plural folding) and tuned for English text. Terms in fewer than 2 reviews are omitted.
- Reviews without a rating count toward totals and the reply rate but not toward rating averages. Dates are read as UTC.
- **Max apps** (default 1,000) caps the number of rows and so the cost. If a source cannot be read, the others are still analyzed and the problem is listed in `STATS`.

## Pricing
| Event | Price |
|---|---|
| App summary (one app with its aggregated review summary) | $5.00 per 1,000 |

Failed runs and the demo are free.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Sentiment Analysis](https://apify.com/ucchiii/review-sentiment-analysis) — VADER sentiment scores and a positive / neutral / negative label for each review or comment (offline, no LLM)
- [Google Maps Reviews Analyzer](https://apify.com/ucchiii/reviews-insights) — per-place ratings, trends, owner response rate and top terms from Google Maps reviews
- [App Store Search](https://apify.com/ucchiii/app-store-search) — Apple App Store search, app lookup by ID or URL and top charts via the official iTunes API
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
