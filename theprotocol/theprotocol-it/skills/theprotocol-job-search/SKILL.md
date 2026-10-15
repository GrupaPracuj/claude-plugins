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

## Server UI switch (`ui=off`)

The server can attach its own UI to `search_job_offers` and `get_job_offer_details`. It turns that UI off for a connection when the request carries the query parameter `ui=off` (e.g. `https://grus-api.theprotocol.it/mcp?ui=off`) or the header `X-Grus-Ui: off`. With UI off, the tools come without `_meta.ui` and return only the structured JSON (`structuredContent`), which you present yourself as described below.

- This plugin is meant to connect with `ui=off`. If the user sets up the connector by hand (e.g. a custom connector in claude.ai or `claude mcp add`), tell them to use the URL with `?ui=off` or to send the `X-Grus-Ui: off` header.
- The switch is part of the connection, not a tool argument — never pass `ui` to any tool.

## How results are shown

Present results yourself in text, using the templates in `assets/` — [offer-list-template.md](assets/offer-list-template.md) for search results and [offer-details-template.md](assets/offer-details-template.md) for offer details (see [Offer details](#offer-details)):

- **Read the template before presenting the first results** in the conversation and follow it exactly: one block per offer — the offer title as a `##` heading, then one paragraph with salary lines starting with `&emsp;•&emsp;` (amounts in bold, units normalized to one spelling; no salary lines when the offer gives no salary) and, as its last line, the offer link hidden behind the text `Zobacz na theprotocol.it` (`View on theprotocol.it` when you reply in English).
- One salary per line, lines joined with a hard line break (`\` at the end), no empty lines inside the block. Not a Markdown list. No table, no HTML tags (`<br>` etc.; `&emsp;` is the only allowed entity), no other lines in the block, no separate list of the same offers.
- After the list of offers, write a short comment (2–4 sentences): how many offers matched in total (`totalCount`), which filters you applied (in plain words), and one useful next step — narrow down (seniority, city, salary, contract), show the next page, or open details of a promising offer.
- If the user asks a concrete question the listing doesn't answer (e.g. "which of these are B2B above 150 zł/h?", "does this offer require Kubernetes?"), answer it from the JSON — briefly and only with data actually present in the response.

Write your own text (comments, answers, questions) in the language the user writes in. Show offer details — labels and all content — in the offer's own language (see the details template); never translate them. The offer list has no language info, so its link text and salary units follow the user's language while titles stay as returned. If the user asks for a translation, give it separately, below the offer.

## Searching

1. **Map the request to filters.** Most filters take dictionary codes, not free text. Call `get_dictionary_elements` for that dictionary.
   - Technology, role or free text → `keywords` (matches title, technology, location and company name — so results are broad; e.g. "Python" also returns analyst or PostgreSQL roles mentioning it).
   - City → `cities` (plain names, e.g. `"Warszawa"`, `"Kraków"`). Voivodeship / "abroad" → `regionsOfWorld` codes.
   - Remote / hybrid / office → `workModeCodes`: `home-office`, `hybrid`, `full-office`, `mobile`. Polish words like `"zdalna"` return 0 results even though search results display work modes that way.
   - Seniority → `positionLevelIds` as integers (junior 17, mid/regular 4, senior 18, expert 19, …).
   - Contract → `typesOfContractIds` (umowa o pracę 0, B2B 3, zlecenie 2, …).
   - Area (backend, frontend, DevOps, QA, AI/ML…) → `itSpecializationsCodes`.
   - Company → `employerNames`. Minimum pay → `salaryFrom` (see caveat below).
   - Offers with disclosed salaries, when the user asks for offers "with salary" or "with salary ranges" without specifying a minimum amount → `salaryFrom: 1`. If the user provides an explicit minimum amount, use that amount instead.
2. **Page size is at least 10.** Use `pageSize: 10` by default. If the user requests fewer than 10 results, still send `pageSize: 10`; for larger requests, use the requested value up to the server maximum of 50. For "more"/"next", repeat the same filters with `pageNumber + 1`.
3. **Don't over-ask.** If the request is searchable as is ("oferty z Reactem"), search first, then suggest refinements. Ask a question only when the request is too vague to search at all.
4. **0 results:** say so, then relax the most restrictive filter (usually salary, seniority or city) and try once more, telling the user what you changed.

### Salary caveat

`salaryFrom` is a single number compared against offer salaries regardless of time unit, contract or gross/net. In practice it behaves like a monthly amount (e.g. 25000 → offers whose range reaches 25k/month), and hourly offers (e.g. 150 zł/h) may be filtered unpredictably. When the user gives an hourly rate or mixes B2B net and employment gross, mention this briefly and, if needed, filter the returned offers yourself from the JSON.

## Offer details

- Call `get_job_offer_details` with the offer's **`groupId`** — never the `offerId` (they look alike; `offerId` fails).
- When the user says "this one", "the second one", "the Scalo offer", resolve it from the most recent search results in the conversation.
- Present the offer using [assets/offer-details-template.md](assets/offer-details-template.md) — read it before showing the first offer details in the conversation. It mirrors the widget's details view: title, badges (in the offer's language), company, publication dates, labelled salary list (same amount format as the offer list), parameters (level, work mode, location, schedule, specialization), text sections in a fixed order, and the `offerUrl` link behind `Zobacz na theprotocol.it` (`View on theprotocol.it` for English offers).
- After the details, add a brief comment only if it helps: answer the user's question, point out key requirements, or note that the offer expires soon.

## Accuracy

Use only what the tools return. Don't invent salaries, benefits, remote policies or requirements that aren't in the data; if something isn't specified (e.g. no salary), say it isn't given in the offer.
