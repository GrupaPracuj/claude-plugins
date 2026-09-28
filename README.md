# Grupa Pracuj — Claude plugins

Official [Claude](https://claude.com/) plugins from Grupa Pracuj S.A., distributed as the `grupa-pracuj` plugin marketplace.

## Plugins

| Plugin | Description |
|---|---|
| [theprotocol-it](theprotocol/theprotocol-it) | Search, compare and view current IT job offers published on [theprotocol.it](https://theprotocol.it/) in Poland. |

## Installation

Add the marketplace:

```bash
claude plugin marketplace add GrupaPracuj/claude-plugins
```

Install a plugin:

```bash
claude plugin install theprotocol-it@grupa-pracuj
```

## Repository layout

```
.claude-plugin/marketplace.json   # marketplace manifest listing all plugins
theprotocol/
└── theprotocol-it/               # plugin: manifest, MCP config, skills
```

To add a plugin, create its directory under the brand folder and add an entry to `plugins` in `.claude-plugin/marketplace.json`. Validate before publishing:

```bash
claude plugin validate --strict .
```

## License

[Apache License 2.0](LICENSE)
