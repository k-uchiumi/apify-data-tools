# Japan Address Normalizer: 住所 → Prefecture, City, Lat/Lng

**Run it on Apify:** https://apify.com/ucchiii/japan-address-normalizer · **Price:** $1.00 per 1,000 address normalizeds · free demo with empty input

**For data teams, e-commerce, logistics and CRM engineers who have messy Japanese addresses** (full-width digits, kanji numerals, 丁目/番地 variants, missing prefecture, 〒 codes, building names stuck on the end) and need clean, comparable fields plus coordinates. This Actor splits every address into prefecture / city / town (町丁目) / the rest, adds latitude and longitude, and tells you how far it could match. **$1 per 1,000 addresses** (only addresses matched to at least a prefecture are charged).

日本の住所を 都道府県・市区町村・町丁目・残り（番地・号・建物）に分け、緯度経度とマッチレベルを付けます。全角・漢数字・丁目/番地の表記ゆれ・〒にも対応。**1,000件あたり $1（都道府県以上に一致した住所のみ課金）。**

**Try it free: leave input empty and press Start** (three sample addresses, not charged, rows carry `_demo: true`).

## Sample output

| input | normalized | pref | city | town | addr | lat / lng | level | status |
|---|---|---|---|---|---|---|---|---|
| `北海道札幌市西区24-2-2-3-3` | 北海道札幌市西区二十四軒二条二丁目3-3 | 北海道 | 札幌市西区 | 二十四軒二条二丁目 | 3-3 | 43.074273 / 141.315099 | 3 | ok |
| `〒060-0001 札幌市中央区北一条西二丁目` | 北海道札幌市中央区北一条西二丁目 | 北海道 | 札幌市中央区 | 北一条西二丁目 | | town point | 3 | ok |
| `東京都千代田区ほげほげ1-1` | 東京都千代田区ほげほげ1-1 | 東京都 | 千代田区 | | ほげほげ1-1 | city office point | 2 | partial |
| `Hello` | | | | | | | 0 | error (free) |

The first row is the example from Geolonia's documentation; the other rows illustrate the format. Coordinates always come from Geolonia's data (town representative point at level 3, city office at level 2, prefectural office at level 1; `pointLevel` says which).

## Input

```json
{ "addresses": ["〒100-8111 東京都千代田区千代田1-1", "大阪府大阪市北区梅田３－１－１ 大阪駅"] }
```

Or normalize a field of every item in an Apify dataset (for example the output of a Google Maps or real-estate scraper):

```json
{ "datasetId": "WkzbQMuFYuamGv3YF", "addressField": "address", "maxItems": 1000, "keepSourceFields": true }
```

With `keepSourceFields`, each output row carries the original item as `source` so you can join results back.

## Output (one row per input)

| Field | Meaning |
|---|---|
| `input` | The address as you gave it |
| `normalized` | One string: pref + city + town + addr (postal code excluded) |
| `pref`, `city`, `town` | Prefecture, city/ward (with 郡 / 政令市 ward), town (大字・丁目, e.g. 二十四軒二条二丁目) |
| `addr` | The rest: block/lot numbers normalized to `3-3`, plus any building name |
| `lat`, `lng` | Coordinates (WGS84) of the most precise matched level; `null` at level 0 |
| `pointLevel` | 1 prefecture office, 2 city office, 3 town representative point |
| `level` | 0 nothing recognized, 1 prefecture, 2 + city, 3 + town (as in Geolonia; Geolonia's level 8 lot lookup is not included) |
| `postalCode` | `NNN-NNNN` if a 〒 code (or a leading 7-digit code) was found, else empty |
| `status` | `ok` = level 3, `partial` = level 1 or 2, `error` = level 0, empty/too long, or data unavailable |
| `error` | Reason for `partial` / `error` |
| `attribution` | "Address data: Geolonia (CC BY 4.0)" |

## What gets normalized

- Full-width letters/digits and half-width kana → standard forms; all hyphen variants (－ ー ‐ ―) → `-`
- Kanji numerals: 二十四軒二条 / 丁目 numbers match the registry; block and lot numbers become Arabic numerals (`九番地一号` → `9-1`)
- 丁目 / 番地 / 号 / の variants: `2番地3号`, `2-3`, `二の三` all end up as `2-3`-style
- Omitted prefecture (`札幌市中央区…`), omitted 郡, omitted 町 in town names, old/new kanji (櫻/桜, 渕/淵 …), Kyoto street names (通り名) skipped
- 〒 postal codes are split off into `postalCode`

## Pricing

**$0.001 per address normalized with level >= 1** ($1 per 1,000). Level 0 results, empty or invalid addresses and errors are free, and so is the demo. Set a maximum budget in Apify to cap spending; the Actor stops when it is reached. Example: 10,000 addresses, 9,500 matched = $9.50.

## Limits and notes

- Up to 10,000 addresses per run (50,000 dataset items). Addresses longer than 500 characters are returned as errors.
- Matching uses the town list per city; areas without 住居表示 or rural 字 can stop at level 2. Put the building name last, or split it off beforehand.
- It does not return lot-level (level 8) coordinates, only town-level representative points.
- Address data is read from Geolonia's free public API (`japanese-addresses-v2.geoloniamaps.com`): one request for the prefecture list plus one per city that appears in your list, cached for the run, 4 connections at most, with backoff on 429/5xx. Geolonia states it may change or stop this API; if it is unreachable the run fails without charge (or rows become free errors).
- Your full addresses are never sent to Geolonia; matching happens inside the Actor.

## Credits and licenses

- Address data: **Geolonia** [japanese-addresses-v2](https://github.com/geolonia/japanese-addresses-v2), created by Geolonia Inc. from the Address Base Registry (アドレス・ベース・レジストリ) of Japan's Digital Agency, licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). The data was processed here (matched, normalized, representative points attached).
- Code: the normalization logic is a Python port of [@geolonia/normalize-japanese-addresses](https://github.com/geolonia/normalize-japanese-addresses), **MIT License, Copyright 2020 Geolonia Inc.** The full notice is in LEGAL.md. Thanks to [Geolonia](https://geolonia.com/).
- This Actor is not affiliated with or endorsed by Geolonia or the Digital Agency. If you publish the results, please keep the credit to Geolonia (CC BY 4.0 requires attribution).

出典: 住所データ © 株式会社Geolonia（CC BY 4.0、「アドレス・ベース・レジストリ」（デジタル庁）をもとに作成）／ ロジック: @geolonia/normalize-japanese-addresses（MIT）。結果を公開する場合は出典表示を残してください。

## 日本語

**住所を「都道府県／市区町村／町丁目／残り」に分けて緯度経度を付ける** Actor です。住所リストを貼り付けるか、他の Actor が出力した Apify データセットの住所項目（`addressField`）を指定してください。入力を空にして Start すると、見本3件で無料デモが動きます。料金は都道府県以上に一致した住所1件あたり $0.001（1,000件で $1）。レベル0・エラー・デモは無料です。レベルは 0=判別不可、1=都道府県、2=市区町村、3=町丁目まで。

<!-- more-tools:start -->
## More tools from the same developer

- [Japanese Text Toolkit](https://apify.com/ucchiii/japanese-text-toolkit) — furigana, romaji, wareki dates and normalization for Japanese text
- [Fuzzy Dedupe](https://apify.com/ucchiii/dataset-fuzzy-dedupe) — find near-duplicate names, addresses, phones and URLs that exact dedup misses
- [CSV / XLSX Converter](https://apify.com/ucchiii/csv-xlsx-converter) — convert CSV, TSV, Excel XLSX, JSON and NDJSON files or Apify datasets into each other
- [Google Maps Leads Cleaner](https://apify.com/ucchiii/maps-leads-cleaner) — dedupe Google Maps scraper results, format phones (E.164), CRM-ready leads
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
