---
name: theprotocol-job-search
description: Searches and displays IT job offers from theprotocol.it using the the:protocol MCP server (search_job_offers, get_job_offer_details, get_dictionary_elements). Use this skill whenever the user wants to find, browse or look up job offers, vacancies or IT roles — e.g. "znajdź oferty dla senior Java developera w Krakowie", "szukam pracy zdalnej z Pythonem", "pokaż szczegóły tej oferty", "jakie są oferty w firmie X", "find remote React jobs over 20k" — even if theprotocol.it is not mentioned by name, and whenever the user asks about requirements, salary or work mode of a specific offer from the results. Do not use it for non-IT jobs, writing CVs or cover letters, or general career advice without a request for offers.
---

# the:protocol job search

Help the user find IT job offers on theprotocol.it and look at their details. The MCP server has three tools:

| Tool | Purpose |
|---|---|
| `search_job_offers` | Filtered, paginated search (max 50 per page). Returns `totalCount` plus offers with `offerId`, `groupId`, `url`, title, company, locations, work modes, contracts with salary, technologies. |
| `get_job_offer_details` | Full offer by `groupId`: responsibilities, requirements (expected/optional), technologies, benefits, recruitment stages, project description, publication and expiry dates. |
| `get_dictionary_elements` | Codes for filters: ContractType, PositionLevel, WorkSchedule, TimeUnit, Currency, SalaryKind, WorkMode, ItCompetence, ItSpecialization, Region, ModelOfPayment. |

## How results are shown

The server renders its own interactive UI (MCP Apps widget) in the user's app for search results and offer details. You only receive the JSON; the user sees nicely formatted cards. So:

- **Do not re-list the offers in text** (no tables, no bullet list of titles/salaries). Repeating what the widget already shows is noise.
- Under the widget, write a short comment (2–4 sentences): how many offers matched in total (`totalCount`), which filters you applied (in plain words), and one useful next step — narrow down (seniority, city, salary, contract), show the next page, or open details of a promising offer.
- Refer to individual offers by title + company when useful ("the Scalo offer looks closest to what you described"), but don't duplicate their data.
- If the user asks a concrete question the widget doesn't answer directly (e.g. "which of these are B2B above 150 zł/h?", "does this offer require Kubernetes?"), answer it from the JSON — briefly and only with data actually present in the response.

Reply in the language the user writes in. Offer content is mostly Polish; translate/summarise when the user writes in another language.

## Searching

1. **Map the request to filters.** Most filters take dictionary codes, not free text. Call `get_dictionary_elements` for that dictionary.
   - Technology, role or free text → `keywords` (matches title, technology, location and company name — so results are broad; e.g. "Python" also returns analyst or PostgreSQL roles mentioning it).
   - City → `cities` (plain names, e.g. `"Warszawa"`, `"Kraków"`). Voivodeship / "abroad" → `regionsOfWorld` codes.
   - Remote / hybrid / office → `workModeCodes`: `home-office`, `hybrid`, `full-office`, `mobile`. Polish words like `"zdalna"` return 0 results even though search results display work modes that way.
   - Seniority → `positionLevelIds` as integers (junior 17, mid/regular 4, senior 18, expert 19, …).
   - Contract → `typesOfContractIds` (umowa o pracę 0, B2B 3, zlecenie 2, …).
   - Area (backend, frontend, DevOps, QA, AI/ML…) → `itSpecializationsCodes`.
   - Company → `employerNames`. Minimum pay → `salaryFrom` (see caveat below).
2. **Default page size is 10** (`pageSize: 10`) unless the user asks for more. For "more"/"next", repeat the same filters with `pageNumber + 1`.
3. **Don't over-ask.** If the request is searchable as is ("oferty z Reactem"), search first, then suggest refinements. Ask a question only when the request is too vague to search at all.
4. **0 results:** say so, then relax the most restrictive filter (usually salary, seniority or city) and try once more, telling the user what you changed.

### Salary caveat

`salaryFrom` is a single number compared against offer salaries regardless of time unit, contract or gross/net. In practice it behaves like a monthly amount (e.g. 25000 → offers whose range reaches 25k/month), and hourly offers (e.g. 150 zł/h) may be filtered unpredictably. When the user gives an hourly rate or mixes B2B net and employment gross, mention this briefly and, if needed, filter the returned offers yourself from the JSON.

## Offer details

- Call `get_job_offer_details` with the offer's **`groupId`** — never the `offerId` (they look alike; `offerId` fails).
- When the user says "this one", "the second one", "the Scalo offer", resolve it from the most recent search results in the conversation.
- The widget shows the offer. Add a brief comment only if it helps: answer the user's question, point out key requirements, or note the expiry date (`archivizationDateUtc`) if it's close. Always make it easy to apply by referring to the offer link (`offerUrl`) when the user wants to apply.

## Accuracy

Use only what the tools return. Don't invent salaries, benefits, remote policies or requirements that aren't in the data; if something isn't specified (e.g. no salary), say it isn't given in the offer.
