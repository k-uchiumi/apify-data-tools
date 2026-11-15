# Sentiment Analysis: Review & Comment Sentiment Scores

**Run it on Apify:** https://apify.com/ucchiii/review-sentiment-analysis · **Price:** $0.30 per 1,000 texts · free demo with empty input

**Sentiment analysis for reviews and comments: one row per text with the VADER compound, positive, neutral and negative scores and a positive / neutral / negative label — plus a summary with the share of each label.**

| id | text | compound | positive | neutral | negative | label |
|---|---|---|---|---|---|---|
| demo-1 | Absolutely loved this place! The staff were friendly and the coffee was amazing. | 0.9097 | 0.535 | 0.465 | 0.0 | positive |
| demo-2 | Terrible service. We waited 40 minutes and the food was cold. Never again. | -0.4767 | 0.0 | 0.795 | 0.205 | negative |
| demo-3 | The shop opens at 9am and is next to the train station. | 0.0 | 0.0 | 1.0 | 0.0 | neutral |
| demo-4 | Good value for money, but the room was a bit noisy at night. | 0.1531 | 0.232 | 0.637 | 0.131 | positive |
| demo-6 | The product broke after two days. Very disappointed and support did NOT help. | -0.6691 | 0.145 | 0.441 | 0.414 | negative |

*(real output of the free demo: 8 sample reviews written for the demo; the full rows also carry `textLength`, `lowConfidence`, `languageNote` and `source`)*

**Try it free:** leave **Texts** and **Dataset** empty and press **Start**. You get the 8-review demo above in a few seconds, with a `SUMMARY` record. Not charged; rows carry `_demo: true`.

**Fully offline and rule-based.** Scoring uses [VADER](https://github.com/cjhutto/vaderSentiment) (Valence Aware Dictionary and sEntiment Reasoner), a lexicon and rule-based model tuned for reviews and social-media-style English. No LLM, no external API, no API key: your texts never leave the run, and the same text always gets the same score.

## Who it is for
- **Local-SEO agencies and multi-location brands** that scrape Google Maps, Trustpilot, app store or e-commerce reviews and want a sentiment column next to every review.
- **Product and support teams** sorting comments, survey answers or tickets into positive, neutral and negative.
- **Analysts** who need a cheap, reproducible baseline before (or instead of) paying for an LLM.

Works next to our **[Google Maps Reviews Analyzer](https://apify.com/ucchiii/reviews-insights)** (`reviews-insights`): that Actor gives one insight row per place (stars, trend, owner response rate, top terms); this one scores every single review text. Point both at the same Google Maps reviews scraper dataset.

## What you get
For each text:
- `compound` — the overall score from **-1** (most negative) to **+1** (most positive);
- `positive`, `neutral`, `negative` — the share of the text in each tone (they add up to about 1);
- `label` — **positive** if compound ≥ 0.05, **negative** if compound ≤ -0.05, otherwise **neutral** (the standard VADER thresholds);
- `id` — your ID (from the **ID field**), or the row number / list position;
- `text` — a preview of the text (first 200 characters) and `textLength`;
- `lowConfidence` and `languageNote` — see *Languages* below.

And one **SUMMARY** record in the key-value store: texts read, scored and charged, the **average compound**, the **count and share of each label**, low-confidence rows, and skipped texts by reason (`empty`, `missingField`, `invalid`, `tooLong`).

```json
{
  "scored": 8, "charged": 0, "averageCompound": 0.0662,
  "labels": {"positive": {"count": 3, "share": 0.375}, "neutral": {"count": 2, "share": 0.25}, "negative": {"count": 3, "share": 0.375}},
  "lowConfidence": 1, "skipped": {"empty": 0, "missingField": 0, "invalid": 0, "tooLong": 0}
}
```

VADER understands what simple word counts miss: negation (`not good` is negative), intensifiers and capitals (`VERY GOOD!!!` scores higher than `good`), contrast (`good, but noisy`), common slang and emoticons such as `:)`.

## How to use
**A list of texts** — paste one review or comment per entry in **Texts**:
```json
{"texts": ["Great coffee!", "Bad service.", "Open until 10pm."]}
```

**A reviews scraper dataset** — pick the run in **Dataset** (or paste its dataset ID or URL). **Text field** is `text` by default, which fits Google Maps review scrapers and the usual review datasets; set **ID field** to carry an ID such as `reviewId` into each row:
```json
{"datasetId": "<dataset ID of your reviews scraper run>", "textField": "text", "idField": "reviewId"}
```
A dotted path such as `review.text` works for nested rows. Texts and a dataset can be used together (texts are scored first). Only the scores, the ID and a short preview are written — other fields of the row (such as reviewer names) are not copied.

## Limits
- Up to **100,000 texts per run** and **5,000 characters per text**. Longer texts, empty texts, rows without the text field and values that are not text are **skipped and not charged**; they are counted in the SUMMARY.
- Set a **maximum charge per run** to cap spend: the Actor stops scoring when it is reached.

## Languages
VADER is built for **English**. Other languages written in the Latin alphabet get partial results (only English-like words and emoticons count). Text written mostly in a non-Latin script (Japanese, Chinese, Korean, Russian, Arabic, Hebrew, Greek, Thai, …) is scored too, but the row gets `lowConfidence: true` and a `languageNote`: such text usually comes out as neutral. Translate it first, or filter those rows out.

## Pricing
Pay per event: **$0.0003 per text** scored — one text = one review, comment or sentence = one output row, whatever its length up to 5,000 characters. That is **$0.30 per 1,000 texts**, with no start fee.
Skipped texts (empty, invalid or too long) and the demo (empty input) are **free**.
Store discounts apply automatically: BRONZE −10%, SILVER −20%, GOLD −30%.
On the Apify Free plan, the $5 of monthly platform credit covers about 16,000 texts a month.

## FAQ
**Is it as good as an LLM?** No — VADER is a fast, transparent baseline. It is very good at clear opinions and short reviews, weaker at sarcasm, domain jargon and long mixed texts. In exchange it is cheap, instant, private and reproducible.

**Why is a review with stars scored differently from its stars?** The score comes only from the words. A 3-star review saying "great food, terrible service" can land either side of zero. Comparing the label with the stars is a quick way to find reviews worth reading.

**Does it read my whole dataset?** It reads rows until 100,000 texts or your maximum charge is reached. Reading is free; you pay only for scored texts.

## Licence and attribution
Sentiment scoring uses **vaderSentiment 3.3.2** by C.J. Hutto, <https://github.com/cjhutto/vaderSentiment>, used unchanged under the MIT License. If you publish results, the authors ask you to cite: Hutto, C.J. & Gilbert, E.E. (2014). VADER: A Parsimonious Rule-based Model for Sentiment Analysis of Social Media Text. Eighth International Conference on Weblogs and Social Media (ICWSM-14). Ann Arbor, MI, June 2014.

The licence, as shipped in the package (`LICENSE.txt`):

```
The MIT License (MIT)

Copyright (c) 2016 C.J. Hutto

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

This Actor is not affiliated with or endorsed by the VADER authors.

If this Actor saved you time, a rating on the Store helps others find it.

<!-- more-tools:start -->
## More tools from the same developer

- [Google Maps Reviews Analyzer](https://apify.com/ucchiii/reviews-insights) — per-place ratings, trends, owner response rate and top terms from Google Maps reviews
- [Google Maps Leads Cleaner](https://apify.com/ucchiii/maps-leads-cleaner) — dedupe Google Maps scraper results, format phones (E.164), CRM-ready leads
- [RAG Chunker](https://apify.com/ucchiii/rag-chunker) — split scraped pages into chunks for RAG and vector stores
- [Japanese Text Toolkit](https://apify.com/ucchiii/japanese-text-toolkit) — furigana, romaji, wareki dates and normalization for Japanese text
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
