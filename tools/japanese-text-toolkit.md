# Japanese Text Toolkit: Furigana, Romaji, Wareki Converter

**Run it on Apify:** https://apify.com/ucchiii/japanese-text-toolkit · **Price:** $0.50 per 1,000 texts · free demo with empty input

**For developers and data teams handling Japanese addresses, names and dates:** this Actor converts texts in bulk to kana readings (furigana), Hepburn romaji, western-calendar dates (from wareki), Arabic numerals (from kanji numerals) and normalized full/half-width characters. **$0.50 per 1,000 texts.**

日本語の住所・氏名・日付などを一括で、ふりがな・ローマ字・和暦→西暦・漢数字→算用数字・全角半角正規化に変換します。**1,000件あたり $0.50。**

**English** | [日本語](#日本語)

Convert Japanese text in bulk — paste texts or point it at a dataset from any other Actor.

| Operation | Example input | Output |
|---|---|---|
| Normalize | `ＡＢＣ１２３　ﾃｽﾄ` | `ABC123 テスト` (NFKC: full-width ASCII → half-width, half-width kana → full-width, spaces tidied) |
| Kana reading (furigana) | `東京都千代田区` | `とうきょうとちよだく` / `トウキョウトチヨダク` |
| Romaji (Hepburn) | `彼は東京へ行く` | `kare wa toukyou e iku` (particles は/へ/を → wa/e/o) |
| Wareki → western | `令和五年四月一日`, `R6.4.15`, `平成元年` | `2023-04-01`, `2024-04-15`, `1989` |
| Western → wareki | `2024年1月5日` | `令和6年1月5日` |
| Kanji numerals → digits | `資本金は三千万円`, `第十五回` | `資本金は30000000円`, `第15回` (only words recognized as numbers are converted — place and person names like 千代田区・千葉・五十嵐 stay as they are) |

**Why**: Japanese company names, addresses and documents need readings for sorting/search, romaji for
international systems, and era-date conversion for forms and filings. Doing this usually means installing
a morphological analyzer and dictionaries — this Actor does it via API, in bulk.

## Input
```json
{ "texts": ["株式会社ＡＢＣは令和五年四月一日に設立されました。"], "operations": ["reading", "romaji", "wareki"] }
```
Or process a field of every item in a dataset: `{ "datasetId": "…", "textField": "companyName" }` — results are added to each item as `companyName_jp`.

## Output (one item per text)
```json
{
  "text": "株式会社ＡＢＣは令和五年四月一日に設立。2024年1月5日に増資。",
  "normalized": "株式会社ABCは令和五年四月一日に設立。2024年1月5日に増資。",
  "hiragana": "かぶしきがいしゃABCはれいわごねんしがついちにちにせつりつ。…",
  "romaji": "kabushikigaisha ABC wa reiwa go nen shi gatsu ichi nichi ni setsuritsu. …",
  "warekiDates": [{ "text": "令和五年四月一日", "iso": "2023-04-01" }],
  "seirekiDates": [{ "text": "2024年1月5日", "iso": "2024-01-05", "wareki": "令和6年1月5日" }],
  "textWithSeireki": "株式会社ABCは2023年4月1日に設立。2024年1月5日に増資。",
  "textWithArabicNumerals": "株式会社ABCは令和5年4月1日に設立。2024年1月5日に増資。"
}
```

## Pricing
**$0.0005 per text** ($0.50 per 1,000).

## Limitations
Readings come from a dictionary (Sudachi, core dictionary). Names, rare words and numbers can have
several valid readings (e.g. 一日 = ついたち/いちにち); the most common reading is used. Romaji is Hepburn
without macrons (東京 → toukyou).

---

## 日本語

日本語テキストを一括変換します。テキストを貼り付けるか、他の Actor の出力データセットを指定してください。

- **正規化**：全角英数→半角、半角カナ→全角、空白の整理（NFKC）
- **読み仮名**：ひらがな・カタカナ（例：東京都千代田区 → とうきょうとちよだく）
- **ローマ字**：ヘボン式（助詞の「は・へ・を」は wa / e / o）
- **和暦⇄西暦**：令和五年四月一日・R6.4.15・平成元年 → 西暦。西暦の日付には和暦を付与
- **漢数字→算用数字**：三千万円 → 30000000円、第十五回 → 第15回（数として使われている語だけを変換。千代田区・千葉・五十嵐などの地名・人名はそのまま）

料金：**1件 $0.0005**（1,000件 $0.50）。

注意：読みは辞書（Sudachi）にもとづきます。人名・固有名詞・数字は読みが複数ありうるため、一般的な読みになります。

## Related tools
- [maps-leads-cleaner](https://apify.com/ucchiii/maps-leads-cleaner) — Clean Google Maps leads, then convert Japanese names and addresses here
- [dataset-dedup-merge](https://apify.com/ucchiii/dataset-dedup-merge) — Merge and dedupe datasets before or after conversion
- [rss-feed-reader](https://apify.com/ucchiii/rss-feed-reader) — Collect Japanese news feeds for text processing

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
