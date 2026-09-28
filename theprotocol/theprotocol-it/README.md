# theprotocol.it

Search and browse current IT job offers published on [theprotocol.it](https://theprotocol.it/) — Poland's IT job board — directly from Claude.

## What's included

| Component | Details |
|---|---|
| MCP server | `theprotocol.it` → `https://grus-api.theprotocol.it/mcp` (streamable HTTP, no authentication) |
| Skill | `theprotocol-job-search` — maps natural-language requests to search filters, shows results and offer details |

### MCP tools (all read-only)

- `search_job_offers` — filtered, paginated search by keywords/technology, city, region, work mode, seniority, contract type, IT specialization, employer and minimum salary.
- `get_job_offer_details` — full offer: responsibilities, requirements, technologies, benefits, recruitment stages, dates and link.
- `get_dictionary_elements` — filter codes (work modes, position levels, contract types, specializations, regions, …).

Results and offer details are rendered by the server as interactive cards (MCP Apps); Claude adds a short comment and suggests how to narrow the search.

## Installation

```bash
claude plugin marketplace add GrupaPracuj/claude-plugins
claude plugin install theprotocol-it@grupa-pracuj
```

Or load locally for testing:

```bash
claude --plugin-dir ./theprotocol-it
```

## Example prompts

- "Find remote offers for a senior Python developer"
- "Pokaż oferty B2B z Reactem w Krakowie"
- "Show details of the second offer — do they require Kubernetes?"

## Scope

The plugin only searches IT job offers on theprotocol.it. It does not write CVs or cover letters, give general career advice, or apply on your behalf — to apply, use the offer link.

## Privacy and terms

The plugin sends only search filters and offer identifiers to the theprotocol.it API; no personal data is required.

- Privacy policy: https://theprotocol.it/polityka-prywatnosci
- Terms of service: https://theprotocol.it/regulamin
- Support: https://hr.theprotocol.it/

## License

[Apache License 2.0](LICENSE)
