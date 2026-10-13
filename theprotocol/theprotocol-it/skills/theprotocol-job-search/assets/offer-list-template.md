# Offer list template

Use this template to present results of `search_job_offers`. One block per offer, in the order returned by the tool. For `get_job_offer_details` use [offer-details-template.md](offer-details-template.md).

## Template

```markdown
## {title}
&emsp;•&emsp;{salary line}\
&emsp;•&emsp;{salary line}\
[{label.cta}]({url})
```

- The title is a level-2 heading (`##`) — never a table, a bold line or a different heading level.
- Below the title, the salaries and the link form **one paragraph** — no empty lines inside the block (an empty line starts a new paragraph and adds a visible gap).
- Salaries: one contract per line, each line starting with exactly `&emsp;•&emsp;` — the entity `&emsp;`, the bullet character `•` (U+2022), the entity `&emsp;` again, then the amount with no extra space. The entities give the indent and the gap after the bullet; plain spaces don't (they are stripped or collapsed). Write `&emsp;` as this exact entity text, never as a literal em space character. **Not** a Markdown list: never start the line with `-`, `*` or `+`. Never put two salaries in one line.
- Every salary line ends with a hard line break: a backslash `\` at the very end of the line. Without it the next line is joined with a space.
- The link is the last line of the paragraph, without `•` and without a trailing `\`.
- Offer with no salary in the data → no salary lines (no "brak" / "n/a"); the link line follows the title directly.
- Never use HTML tags (`<br>`, `<span>` etc.); the `&emsp;` entity in salary lines is the only allowed entity. Never present the offers as a table. Don't add other lines to the block or a separate list of the same offers.

## Fields

| Placeholder | Source | Rules |
|---|---|---|
| `{title}` | `title` | Copy as returned. Escape a leading `#` with `\` if present. |
| `{salary line}` | `contracts[]` with `salary.from` and/or `salary.to` | One line per contract: `**{from}–{to}** {currency} {kind}/{timeUnit} ({contract})`. Bold only the amount range — never the currency or the rest. Amounts grouped by thousands with a regular space (`21 700`). Only one bound, or `to` ≤ `from` → a single amount (`**180** zł…`, never `180–180`). Parts below are normalized; a missing part is skipped. |
| `{url}` | `url` | Always behind the `{label.cta}` text, never as a bare URL. |

### Salary parts

Data from `search_job_offers` is inconsistent (mixed Polish and English values). Normalize every part with the table below, so that one listing uses one spelling throughout. Pick the PL or EN column by the language of your reply (English → EN, otherwise PL). Values not listed in the table: copy as returned.

| Part | Field | Value in data | PL | EN |
|---|---|---|---|---|
| `{currency}` | `salary.currency` | `zł` | `zł` | `PLN` |
| `{kind}` | `salary.kindName`, if missing `salary.kind` | `brutto`, `gross` | `brutto` | `gross` |
| | | `netto (+ VAT)`, `net (+ VAT)` | `netto (+ VAT)` | `net (+ VAT)` |
| `{timeUnit}` | `salary.timeUnitName`, if missing `salary.timeUnit` | `miesięcznie`, `monthly` | `mies.` | `mth.` |
| | | `godzinowo`, `hourly` | `godz.` | `hr.` |
| `{contract}` | `typeName` (always Polish) | `umowa o pracę` | `UoP` | `CoE` |
| | | `umowa o pracę tymczasową` | `UoPT` | `TEC` |
| | | `umowa zlecenie` | `UZ` | `CoM` |
| | | `umowa o dzieło` | `UoD` | `CfSW` |
| | | `umowa agencyjna` | `UA` | `AC` |
| | | `umowa o staż`, `umowa stażowa` | `UoS` | `IC` |
| | | `kontrakt B2B`, `B2B` | `B2B` | `B2B` |

Any other contract name → initials of its words (same rule as in [offer-details-template.md](offer-details-template.md)).

## Language

Search results don't include the offer's `language`, so pick by the language of your reply: English → EN, otherwise PL. Use the same choice for the whole listing.

| Label | PL | EN |
|---|---|---|
| `label.cta` | Zobacz na theprotocol.it | View on theprotocol.it |

`label.cta` is the same as the CTA in [offer-details-template.md](offer-details-template.md). For other reply languages translate the CTA; salary parts follow the PL/EN choice (see [Salary parts](#salary-parts)). Titles stay as returned.

## Example

```markdown
## Senior Data Designer
&emsp;•&emsp;**25 000–29 500** zł brutto/mies. (UoP)\
&emsp;•&emsp;**24 000–36 000** zł netto (+ VAT)/mies. (B2B)\
[Zobacz na theprotocol.it](https://theprotocol.it/szczegoly/praca/...)

## Senior Data Scientist (ML and Fraud Detection)
&emsp;•&emsp;**180** zł netto (+ VAT)/godz. (B2B)\
[Zobacz na theprotocol.it](https://theprotocol.it/szczegoly/praca/...)

## Mid Data Engineer
[Zobacz na theprotocol.it](https://theprotocol.it/szczegoly/praca/...)
```

English reply:

```markdown
## Senior GCP Cloud DevOps Engineer
&emsp;•&emsp;**21 700–26 100** PLN gross/mth. (CoE)\
&emsp;•&emsp;**30 240–36 330** PLN net (+ VAT)/mth. (B2B)\
[View on theprotocol.it](https://theprotocol.it/szczegoly/praca/...)

## Python AI Engineer
&emsp;•&emsp;**180** PLN net (+ VAT)/hr. (B2B)\
[View on theprotocol.it](https://theprotocol.it/szczegoly/praca/...)
```
