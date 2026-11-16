# Reviews Analyzer for Trustpilot, Booking, G2 Data

**Run it on Apify:** https://apify.com/ucchiii/review-dataset-analyzer · **Price:** $5.00 per 1,000 business summaries · free demo with empty input

**Reviews analyzer for Trustpilot, Booking and G2 style data: one summary row per business from thousands of reviews**

| rank | businessName | reviewsCount | avgStars | last90DaysAvgStars | replyRate | avgReplyDays | top positive terms | top negative terms |
|---|---|---|---|---|---|---|---|---|
| 1 | Demo Harbor Hotel (fictional) | 6 | 4.0 | 4.75 | 0.833 | 1.6 | breakfast, great breakfast, reception | |
| 2 | Sample Coffee Roasters (fictional) | 6 | 3.5 | 4.0 | 0.667 | 2.8 | great, atmosphere, coffee | coffee, cold, service |
| 3 | Example Ramen House (fictional) | 6 | 3.33 | 3.75 | 0.333 | 1.5 | broth, noodle, perfect | long, long wait, overpriced |

*(real output of the free demo: 18 fictional reviews of 3 sample businesses; each row also has the star distribution, the monthly `trend`, median reply days and two more ranks)*

**For agencies, reputation analysts, hotel and SaaS marketers and market researchers who already have reviews in a table.** You (or your scraper) collected
**thousands of review rows, and nobody has time to read them**. This Actor turns them into **one summary row per business, hotel or product**, and ranks the businesses
against each other when you put several in. It is rule-based and reproducible: no scraping, no proxies, no LLM, no API key.

You bring the reviews. It reads:
- an **Apify dataset** from a reviews run in your own account (pick it in **Reviews datasets**; the field lists your datasets, no ID to copy), and/or
- a **public CSV, JSON or NDJSON file** by URL (**File URLs**).

It does not access Trustpilot, Booking, G2 or any other site, and it is not affiliated with them. The names only describe which column layouts it knows.

**Try it:** press **Start** with the input as it is (nothing picked). You get the free demo above in a few seconds: 3 rows from 18 sample reviews, plus `STATS`. Not charged.

**Price:** from **$3.50 per 1,000 business summaries** on Apify's paid plans (the price shown on this page); $5.00 per 1,000 on the free plan.
One business = one output row; reading reviews is free.

For each business you get:
- **Rating**: review count, average stars (10-point scores are converted to 5 stars), distribution (1 to 5 stars)
- **Momentum**: reviews in the last 30 days, average stars over the last 90 days, a **trend** (count and average stars per month, or per N months)
- **Reply behaviour**: reply rate, average and median days to reply
- **What people say**: top **positive** terms and top **negative** terms, single words and two-word phrases, English stop words removed (add your own). Booking-style "liked" / "disliked" and G2-style "pros" / "cons" columns count as positive / negative whatever the stars are
- **Comparison**: with two or more businesses, `rank` (by average stars), `rankByReplyRate` and `rankByRecentStars`, and rows sorted by rank

**Privacy by design:** reviewer names, IDs, profile links, nationalities and the review text are never read into the output. Only aggregates are written, and the column tables do not contain any reviewer column.

## Column mapping
The Actor looks for these columns (case, `_`, `-` and spaces do not matter; `a.b` means a nested field). The style you pick in **Source style** is tried first, then the others (**Detect** tries all).

| Meaning | Trustpilot style | Booking style | G2 style |
|---|---|---|---|
| Business | `businessName`, `companyName`, `businessUnit.displayName`, `domain` | `hotelName`, `propertyName` | `productName`, `product`, `vendorName` |
| Business ID | `businessUnitId`, `businessId` | `hotelId`, `propertyId` | `productId`, `productSlug` |
| Rating | `rating`, `stars` | `reviewScore` (0 to 10), `score` | `starRating`, `rating` |
| Date | `publishedDate`, `dates.publishedDate`, `date` | `reviewDate`, `date` | `publishedAt`, `reviewDate`, `submittedAt` |
| Text | `text`, `reviewText`, `body` (+ `title`) | `text`, `reviewText` (+ `reviewTitle`) | `reviewText`, `text` (+ `reviewTitle`) |
| Positive part | | `likedText`, `positiveText`, `pros` | `likedBest`, `love`, `pros` |
| Negative part | | `dislikedText`, `negativeText`, `cons` | `dislikes`, `hate`, `cons` |
| Business reply | `reply.message`, `replyText`, `companyReply` | `propertyResponse`, `hotelResponse` | `vendorReply`, `vendorResponse` |
| Reply date | `reply.publishedDate`, `replyDate` | `propertyResponseDate`, `responseDate` | `vendorReplyDate`, `responseDate` |

**Your columns are named differently?** Use the **column overrides** (Business, Rating, Review date, Review text, Positive text, Negative text, Business reply, Reply date). An override is tried before the tables. With **Source style: Custom** only your overrides and the generic names are used.
The `columnsUsed` field of each row (and `STATS`) shows which column was read for each meaning, so you can check the mapping in seconds.

- **Scores:** the **Rating scale** is detected (a `reviewScore` column or the Booking style means 10; otherwise the highest value in the input decides: up to 5, up to 10, else 100). Set it by hand if your data has only low scores.
- **No business column?** Then each dataset or file counts as one business (`businessName` is null).
- **Dates** can be ISO dates, `March 5, 2026` style dates or UNIX times. Reviews without a date still count in totals but not in the trend.

## Input
```json
{
  "datasetIds": ["<dataset ID of your reviews run>"],
  "fileUrls": ["https://example.com/my-reviews.csv"],
  "source": "auto",
  "periodMonths": 1,
  "topTerms": 20,
  "stopwords": ["mybrand"]
}
```

**Few reviews per business?** Scrapers often return only part of the reviews. The Actor still works, but with fewer than 20 reviews the trend and top terms are only indicative; such rows have `lowSample: true`.

## Output (one row per business)
```json
{
  "businessId": "demo-hotel", "businessName": "Demo Harbor Hotel (fictional)", "url": null,
  "reviewsCount": 6, "lowSample": true, "avgStars": 4.0, "starsDistribution": {"1": 0, "2": 1, "3": 1, "4": 1, "5": 3},
  "last30DaysCount": 1, "last90DaysAvgStars": 4.75,
  "trend": [{"period": "2026-08", "count": 1, "avgStars": 5.0}],
  "replyRate": 0.833, "avgReplyDays": 1.6, "medianReplyDays": 1.0,
  "topPositiveTerms": [{"term": "great breakfast", "count": 3}], "topNegativeTerms": [],
  "latestReviewAt": "2026-11-04T21:53:05+00:00",
  "rank": 1, "rankOf": 3, "rankByReplyRate": 1, "rankByRecentStars": 1,
  "columnsUsed": {"business": "businessName", "rating": "rating", "date": "publishedDate", "reply": "reply.message"}
}
```
Run statistics (`reviewsRead`, `reviewsSkipped`, `businesses`, `written`, `inputErrors`, `truncated`, `columnsUsed`) are saved in the key-value store as `STATS`.

## Notes
- Works on data you already have; it does not access any review site. You are responsible for how the underlying data was collected and used.
- A dataset or file that cannot be read (wrong ID, no access, not a public URL, not JSON or CSV) is skipped and listed in `STATS.inputErrors`; the run fails only when nothing could be read at all. File URLs must be public http(s) links; internal network addresses are blocked.
- Up to 200,000 reviews per run (`STATS.truncated` tells you if more were left out); files up to 50 MB each.
- Term analysis is simple (lowercase, basic plural folding) and tuned for English text. Terms appearing in fewer than 2 reviews are omitted.
- Ranks use the average stars as they are, so check `lowSample` before you compare a business with 5 reviews to one with 500. Ties share a rank.
- Reviews without a rating count toward totals and the reply rate but not toward the averages.

## Pricing
| Event | Free plan | Paid plans (Store discount tiers Bronze / Silver / Gold) |
|---|---|---|
| Business summary (one business with its aggregated review summary) | $5.00 per 1,000 | $4.50 / $4.00 / $3.50 per 1,000 |

The price on this page ("from $3.50") is the Gold tier price. Failed runs and the demo are free.
On the Apify Free plan, the $5 of monthly platform credit covers about **1,000 business summaries** a month, which is enough to try it on real data.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Sentiment Analysis](https://apify.com/ucchiii/review-sentiment-analysis) — VADER sentiment scores and a positive / neutral / negative label for each review or comment (offline, no LLM)
- [Google Maps Reviews Analyzer](https://apify.com/ucchiii/reviews-insights) — per-place ratings, trends, owner response rate and top terms from Google Maps reviews
- [Google Maps Market Analyzer](https://apify.com/ucchiii/maps-market-analyzer) — market and competitor tables from Google Maps scraper datasets
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
