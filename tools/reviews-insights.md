# Google Maps Reviews Analyzer: Ratings, Trends, Terms

**Run it on Apify:** https://apify.com/ucchiii/reviews-insights · **Price:** $5.00 per 1,000 places · free demo with empty input

**For local-SEO agencies, multi-location brands and market researchers who scrape Google Maps reviews.** You have
**thousands of rows nobody has time to read**. This Actor turns the output of a reviews scraper
(e.g. `compass/Google-Maps-Reviews-Scraper`) into **one insight row per place** — no scraping, no proxies, no LLM, fully
rule-based and reproducible. **$5 per 1,000 places.**

For each place you get:
- **Rating**: review count, average stars, distribution (1–5 stars)
- **Momentum**: reviews in the last 30 days, average stars over the last 90 days, a **trend** (count and average stars per month or per N months)
- **Owner engagement**: owner response rate and average days to respond
- **What people say**: top **positive** terms (4–5 star reviews) and top **negative** terms (1–2 star reviews), single words and two-word phrases, English stop words removed (add your own)
- **Photos**: share of reviews with photos, and the latest review date

**Privacy by design:** reviewer names, IDs and profile URLs are never written to the output, and neither is the review text — only aggregates.

One place = one output row; reading reviews is free. Leave `datasetIds` empty for a free demo on fictional data.

## Use cases
- Monthly reputation report per location for a multi-location brand
- Compare competitors' ratings, response rates and recurring complaints
- Spot places whose recent rating is dropping

## Input
```json
{
  "datasetIds": ["<dataset ID of your reviews scraper run>"],
  "periodMonths": 1,
  "topTerms": 20,
  "stopwords": ["mybrand"]
}
```

## Output (one row per place)
```json
{
  "placeId": "demo-cafe", "placeName": "Sample Coffee Roasters (fictional)", "url": "https://www.example.com/demo-cafe",
  "reviewsCount": 6, "avgStars": 3.5, "starsDistribution": {"1": 1, "2": 1, "3": 0, "4": 2, "5": 2},
  "last30DaysCount": 3, "last90DaysAvgStars": 3.33,
  "trend": [{"period": "2026-06", "count": 1, "avgStars": 1.0}],
  "ownerResponseRate": 0.667, "avgOwnerResponseDays": 2.8,
  "topPositiveTerms": [{"term": "great coffee", "count": 2}], "topNegativeTerms": [{"term": "slow service", "count": 2}],
  "withPhotosShare": 0.333, "latestReviewAt": "2026-10-15T00:00:00+00:00"
}
```
Run statistics (`reviewsRead`, `reviewsSkipped`, `places`, `written`) are saved in the key-value store as `STATS`.

## Notes
- Works on datasets you already own; it does not access Google. You are responsible for how the underlying data was collected and used.
- Term analysis is simple (lowercase, basic plural folding) and tuned for English text. Translated text (`textTranslated`) is used when the original is empty. Terms appearing in fewer than 2 reviews are omitted.
- Reviews without a place ID are grouped by URL or place name; reviews without a star rating count toward totals but not rating averages.

<!-- more-tools:start -->
## More tools from the same developer

- [Google Maps Leads Cleaner](https://apify.com/ucchiii/maps-leads-cleaner) — dedupe Google Maps scraper results, format phones (E.164), CRM-ready leads
- [Google Maps Market Analyzer](https://apify.com/ucchiii/maps-market-analyzer) — market and competitor tables from Google Maps scraper datasets
- [Google Sheets Export](https://apify.com/ucchiii/google-sheets-export) — export an Apify dataset to Google Sheets
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
