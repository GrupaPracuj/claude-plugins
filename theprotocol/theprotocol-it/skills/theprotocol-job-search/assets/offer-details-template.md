# Offer details template

Use this template to present the result of `get_job_offer_details`. It shows the same data, in the same order, as the offer details view of the theprotocol.it widget. Omit every line or section whose data is missing (the only exception is the title, see below) — never write placeholders like "brak" or guess values.

## Template

```markdown
## {title}

{badges}

**{label.company}** {company}\
**{label.dateFrom}** {publishedFrom} · **{label.dateTo}** {expiresAt}\
**{label.salary}**
- {salary}
- {salary}

**{label.level}** {positionLevels}\
**{label.workMode}** {workModes}\
**{label.location}** {locations}\
**{label.workSchedule}** {workSchedules}\
**{label.specialization}** {specializations}

[{label.cta}]({offerUrl})

### {section title}
- {element}
- {element}

(... further sections ...)

[{label.cta}]({offerUrl})
```

### Header and parameter lines

Two blocks are plain paragraphs of labelled lines — **not bullet lists**:

- header: `{label.company}`, `{label.dateFrom}`, `{label.salary}`,
- parameters: `{label.level}`, `{label.workMode}`, `{label.location}`, `{label.workSchedule}`, `{label.specialization}`.

Many clients join a plain line break into a space, so inside each block every line **except the last one present** ends with a hard line break: a backslash `\` at the very end of the line. The last line of a block has **no** `\` (it would show as a literal character) — e.g. `{label.salary}` when the offer has salary, otherwise the dates line; in the parameters, whichever line comes last after omitting missing ones. Never use `<br>` or any other HTML, and never use two trailing spaces as a line break. Keep an empty line between the salary list and the parameters.

## Fields

All paths are relative to the offer object returned by `get_job_offer_details`.

| Placeholder | Source | Rules |
|---|---|---|
| `{title}` | `attributes.title.value` | Missing → `Oferta pracy` for a PL offer, `Job offer` for an EN offer. |
| `{badges}` | `badges.new` or `publicationDetails.new` → `` `{label.badgeNew}` ``; `badges.immediateEmployment` → `` `{label.badgeImmediate}` `` | Join with ` · `. Followed by an empty line, so the badges form their own paragraph. No badges → omit the line **and** the empty line after it (the block starts with `{label.company}`). |
| `{company}` | `attributes.employer.name` | |
| `{publishedFrom}` / `{expiresAt}` | `publicationDetails.dateOfInitialPublicationUtc` / `publicationDetails.archivizationDateUtc` | Format as a long date in UTC, e.g. `7 października 2026` (`7 October 2026` in English). Only the start date known → write `–` for the end and drop the `{label.dateTo}` part; neither known → omit the line. |
| `{salary}` | `attributes.employment.typesOfContracts[]` | One bullet per contract with `salary.from` and/or `salary.to`, in the same format as the offer list: `**{from}–{to}** {currency} {kindName}/{timeUnit.shortForm} ({contract abbreviation})`, e.g. `**18 000–24 000** zł brutto/mies. (UoP)`. Bold only the amount range. Amounts grouped by thousands with a regular space. Only one bound, or `to` ≤ `from` → a single amount (`**180**`, never `180–180`). `{currency}` comes from `currencyCode`, not `currencyName`: `PLN` → `zł` for a PL offer, `PLN` for an EN offer; other codes as returned. `kindName` missing → `kindCode`. Skip any part that is missing. No contract with salary → omit the `{label.salary}` line and all its bullets. |
| `{positionLevels}` | `attributes.employment.positionLevels[]` → `name` (fallback `code`) | Join list values with ` • `. Never show raw numeric IDs. |
| `{workModes}` | `attributes.employment.detailedWorkModes[].name` | Join with ` • `. |
| `{locations}` | `partitions[].workplaces[].location` and `attributes.workplaces[].location` | Deduplicate, join with ` • `. |
| `{workSchedules}` | `attributes.employment.workSchedules[].name` | Join with ` • `. |
| `{specializations}` | `attributes.specializations[].name` | Join with ` • `. |
| `{offerUrl}` | `offerUrl` | Always behind the CTA text, never as a bare URL. Shown twice: after the parameters and at the very end. No URL → omit both links. |
| Sections | `textSections[]`, matched by `type` | Render only the types listed below, in that order, regardless of the order in the data. `elements` non-empty → bullet list of elements; otherwise `plainText` as a paragraph; both empty → omit the section. Copy content as returned — don't shorten, merge or reword it (never translate — content stays in the offer's language). |

Contract abbreviations: umowa o pracę → `UoP`, umowa o pracę tymczasową → `UoPT`, umowa zlecenie → `UZ`, umowa o dzieło → `UoD`, umowa agencyjna → `UA`, umowa o staż / umowa stażowa → `UoS`, kontrakt B2B / B2B → `B2B`. English names: contract of employment → `CoE`, temporary employment contract → `TEC`, contract of mandate → `CoM`, contract for specific work → `CfSW`, B2B contract → `B2B`, internship contract → `IC`, agency contract → `AC`. Any other name → initials of its words.

## Section order and titles

| `type` | PL | EN |
|---|---|---|
| `technologies-expected` | Technologie wymagane | Expected technologies |
| `technologies-optional` | Technologie opcjonalne | Optional technologies |
| `technologies-os` | Systemy operacyjne | Operating systems |
| `responsibilities` | Twój zakres obowiązków | Your responsibilities |
| `requirements-expected` | Nasze wymagania | Our requirements |
| `about-project` | O projekcie | About the project |
| `offered` | To oferujemy | What we offer |
| `training-space` | Takie dajemy możliwości rozwoju | Development opportunities we offer |
| `benefits` | Benefity | Benefits |
| `recruitment-stages` | Etapy rekrutacji | Recruitment stages |

## Labels

Like the widget, choose labels by the offer's `language`: starts with `en` → EN, otherwise PL.

| Label | PL | EN |
|---|---|---|
| `label.company` | firma: | company: |
| `label.dateFrom` | od: | from: |
| `label.dateTo` | do: | to: |
| `label.level` | poziom: | level: |
| `label.workMode` | tryb pracy: | work mode: |
| `label.location` | lokalizacja: | location: |
| `label.workSchedule` | rodzaj pracy: | work schedule: |
| `label.specialization` | specjalizacja: | specialization: |
| `label.salary` | zarobki: | salary: |
| `label.badgeNew` | nowa | new |
| `label.badgeImmediate` | praca od zaraz | immediate employment |
| `label.cta` | Zobacz na theprotocol.it | View on theprotocol.it |

## Example

```markdown
## Senior Python Developer

`nowa` · `praca od zaraz`

**firma:** Example Sp. z o.o.\
**od:** 1 października 2026 · **do:** 31 października 2026\
**zarobki:**
- **18 000–24 000** zł brutto/mies. (UoP)
- **140–170** zł netto (+ VAT)/godz. (B2B)

**poziom:** senior (specialist)\
**tryb pracy:** praca hybrydowa\
**lokalizacja:** Warszawa • Kraków\
**rodzaj pracy:** pełny etat\
**specjalizacja:** Backend

[Zobacz na theprotocol.it](https://theprotocol.it/szczegoly/praca/...)

### Technologie wymagane
- Python
- PostgreSQL

### Twój zakres obowiązków
- Projektowanie i rozwój usług backendowych

[Zobacz na theprotocol.it](https://theprotocol.it/szczegoly/praca/...)
```

English offer (`language` starts with `en`):

```markdown
## Senior GCP Cloud DevOps Engineer

`new` · `immediate employment`

**company:** ITDS Polska Sp. z o.o.\
**from:** 7 October 2026 · **to:** 6 November 2026\
**salary:**
- **21 700–26 100** PLN gross/mth. (CoE)
- **30 240–36 330** PLN net (+ VAT)/mth. (B2B)

**level:** senior\
**work mode:** hybrid work\
**location:** Kraków

[View on theprotocol.it](https://theprotocol.it/szczegoly/praca/...)

(... sections ...)

[View on theprotocol.it](https://theprotocol.it/szczegoly/praca/...)
```

Offer without badges and without salary (no empty line at the top, no lone `zarobki:` label; the dates line is now the last header line, so it has no `\`):

```markdown
## Mid Data Engineer

**firma:** Example Sp. z o.o.\
**od:** 1 października 2026 · **do:** 31 października 2026

**poziom:** mid\
**tryb pracy:** praca zdalna

[Zobacz na theprotocol.it](https://theprotocol.it/szczegoly/praca/...)
```
