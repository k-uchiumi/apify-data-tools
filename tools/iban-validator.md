# IBAN Validator: Bulk IBAN Check, BIC & Bank Name

**Run it on Apify:** https://apify.com/ucchiii/iban-validator · **Price:** $0.10 per 1,000 ibans · free demo with empty input

**IBAN validator for whole lists: paste IBANs (or point to a dataset) and get one row per IBAN — valid or not, the exact reason when it is not (length, checksum, country, format), the clean electronic and print formats, country, bank code, BIC and bank name where known, and the SEPA flag.**

| input | valid | iban | errorCodes | countryCode | bankName | bic |
|---|---|---|---|---|---|---|
| DE89370400440532013000 | true | DE89370400440532013000 | | DE | Commerzbank | COBADEFFXXX |
| GB82 WEST 1234 5698 7654 32 | true | GB82WEST12345698765432 | | GB | | |
| fr14 2004 1010 0505 0001 3m02 606 | true | FR1420041010050500013M02606 | | FR | LA BANQUE POSTALE | PSSTFRPP |
| NL91ABNA0417164300 | true | NL91ABNA0417164300 | | NL | ABN AMRO BANK N.V. | ABNANL2A |
| DE89370400440532013001 | false | DE89370400440532013001 | checksum | DE | Commerzbank | COBADEFFXXX |
| GB82WEST1234569876543 | false | GB82WEST1234569876543 | length, checksum | GB | | |

*(real output of the free demo on public example IBANs; the full row has more fields, see Output fields.)*

> **Structure and checksum only — NOT whether the account exists.** A valid IBAN has the right length and layout for its country and correct mod-97 check digits. It can still belong to a closed or never-opened account. Only the bank (or a payment-provider account check) can tell you that.

**Try it free:** leave **IBANs** and **Dataset** empty and press **Start** — 7 example IBANs are checked as a demo (not charged, rows carry `_demo: true`).

## What you get
- **Valid or invalid, with every reason** (`errors`, `errorCodes`):
  - `length` — wrong number of characters for that country (DE has 22, GB 22, FR 27, NL 18, ...);
  - `checksum` — the two check digits do not match (mod-97, ISO 7064): almost always a typo or swapped digits;
  - `country` — the first two letters are not a country that uses IBANs (e.g. `US`), or there is no country code;
  - `format` — characters that cannot be in an IBAN, or the account part does not follow the country's layout (letters where digits must be).
- **Normalised IBAN**: spaces, hyphens, dots and an `IBAN` prefix are removed and letters are upper-cased (`iban: de89 3704-0044 0532 0130 00` → `DE89370400440532013000`), plus the **print format** in groups of four.
- **Parts of the IBAN**: country code and name, bank code, branch (sort) code and account code.
- **BIC and bank name** when the bank registry bundled with schwifty knows the bank code (good coverage for DE, AT, BE, CH, ES, FR, IT, NL, PL and many more; often empty for GB).
- **SEPA flag**: whether the country is in the Single Euro Payments Area.
- **Fully offline and private**: everything is computed inside the run. No bank, API or website is contacted, and nothing is stored except the rows you get back (your input is echoed in `input`).

## How to use
1. **IBANs**: one per line, with or without spaces, any case. Or pick a **Dataset** (for example the output of another Actor, or a CSV you imported) and set **IBAN field** (default `iban`, nested like `bank.iban` works).
2. Press **Start**. Filter the dataset on `valid = false` (or open the **Why invalid** view) to see what to fix.

```json
{
  "ibans": ["DE89 3704 0044 0532 0130 00", "GB82WEST12345698765432"],
  "datasetId": "WkzbQMuFYuamGv3YF",
  "ibanField": "iban"
}
```

## Output fields
- `input` — the value exactly as given; `iban` — normalised electronic format; `formatted` — print format (`DE89 3704 0044 0532 0130 00`).
- `valid` — `true` when length, country format and check digits are all right.
- `errors` — every reason it is invalid, e.g. `checksum: check digits do not match (mod-97 check failed): probably a typo`; `errorCodes` — the short codes (`length`, `checksum`, `country`, `format`). Both empty when valid.
- `countryCode`, `countryName`, `bankCode`, `branchCode`, `accountCode` — the parts are filled only when the length and layout are right (they would be meaningless otherwise), also when only the checksum is wrong.
- `bic`, `bankName` — from schwifty's bundled bank registry; `null` when the bank code is not in it.
- `sepa` — `true` / `false` for the country; `null` when the country does not use IBANs.
- **SUMMARY** record: IBANs checked, valid, invalid and charged, empty values skipped, IBANs over the limit, counts per error reason and per country.

## Limits
- **Does not check that the account exists**, is open, or belongs to a given name (no bank or payment API is called).
- A few countries also have a national check digit inside the account number; this Actor checks the international IBAN check digits, which catch nearly all typos.
- BIC and bank name depend on the registry bundled with the pinned schwifty version and can be missing or out of date for some banks.
- Up to **100,000 IBANs per run**; extra ones are not checked or charged and are counted in the SUMMARY record. Rows keep your input order; duplicates get one row each.
- Set a **maximum charge per run** to cap spend: the Actor checks only as many IBANs as fit and never charges more than that.

## Pricing
Pay per event: **$0.0001 per IBAN checked** — one row. That is **$0.10 per 1,000 IBANs**, with no start fee.
- **Valid and invalid results are both charged**, because each gets a definite answer.
- **Empty values are free**: blank lines in the list and dataset items without the IBAN field are skipped and not charged.
- **The demo (empty input) is free** (`_demo` rows, charge 0), and runs that stop on invalid input (for example a dataset ID that cannot be read) are not charged.

On the Apify Free plan, the $5 of monthly platform credit covers about 50,000 IBANs a month.

## FAQ
**Is my IBAN valid if this says `valid: true`?** It is well-formed: right country, length and layout, and correct check digits. It does not prove that the account exists.

**Why is the BIC empty for my UK IBAN?** The bundled registry has no BIC for many UK sort codes. The IBAN check itself is still complete.

**Can I paste IBANs with spaces or in lower case?** Yes. `gb82 west 1234 5698 7654 32` is read as `GB82WEST12345698765432`.

**Is any data sent anywhere?** No. The check runs inside the Actor with a bundled library; there are no network calls.

## Open-source licence
The validation uses **[schwifty](https://github.com/mdomke/schwifty)** by Martin Domke (version 2026.10.1, unmodified), which bundles the IBAN country specifications and the bank registry. Its licence, quoted from the package:

```
The MIT License (MIT)

Copyright (c) 2021 Martin Domke

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

Country names come from [pycountry](https://github.com/pycountry/pycountry) (LGPL-2.1, used unmodified as a library). This Actor is not affiliated with or endorsed by the schwifty authors, SWIFT or any bank.

<!-- more-tools:start -->
## More tools from the same developer

- [French Companies Search](https://apify.com/ucchiii/france-company-search) — French companies and SIREN/SIRET lookups from the official SIRENE register
- [Filter CSV, JSON or Dataset Rows by Condition](https://apify.com/ucchiii/filter-rows) — keep only the rows of a CSV, JSON file or dataset that match simple conditions like country = US or price > 100
- [Remove Duplicates](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
- [JSON to CSV Converter](https://apify.com/ucchiii/json-to-csv) — turn nested JSON files or datasets into one flat CSV file
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
