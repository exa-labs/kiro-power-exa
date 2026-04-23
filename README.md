# Kiro Power: Exa Web Search & Research

Official [Kiro](https://kiro.dev) power that adds Exa's web search, crawl, and research tools to Kiro agents.

## What it gives you

- **Semantic web search** over the live web, with optional filters by domain, date, and category (company / people)
- **Clean content extraction** from any URL as markdown
- **Code and documentation search** tuned for programming questions
- **Multi-query research** across diverse angles when a single search isn't enough

## Install

In Kiro, add this power from GitHub:

```
exa-labs/kiro-power-exa
```

## Setup

1. Get an API key at [dashboard.exa.ai](https://dashboard.exa.ai). New accounts include free credits.
2. Export `EXA_API_KEY` in the shell Kiro launches from, or add it to Kiro's MCP environment config.
3. Mention one of the trigger keywords (`search`, `research`, `find`, `crawl`, etc.) in your prompt. Kiro activates the power automatically.

## What's in this repo

- `POWER.md` — power manifest and onboarding, loaded by Kiro on activation
- `mcp.json` — MCP server config for `mcp.exa.ai`
- `steering/` — task-specific guidance files (query writing, extraction, filtering, synthesis, source quality, and domain patterns for code / companies / people / news / papers / relationships) that Kiro loads on demand

## Links

- [Exa homepage](https://exa.ai)
- [Exa API docs](https://docs.exa.ai)
- [Exa MCP server](https://github.com/exa-labs/exa-mcp-server)
- [Kiro powers documentation](https://kiro.dev/docs/powers/create/)

## License

[MIT](./LICENSE)
