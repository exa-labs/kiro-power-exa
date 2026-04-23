---
name: "exa"
displayName: "Exa Web Search & Research"
description: "Give Kiro agents a high-quality search, crawl, and research layer over the live web. Find current docs, code examples, companies, and people; extract clean markdown from any URL."
keywords: ["search", "find", "web", "research", "deep dive", "look up", "crawl", "code-search", "rag", "documentation", "competitive analysis", "who is", "what is", "exa", "web-search", "company-research", "linkedin", "crawling"]
author: "Exa"
---

# Exa Web Search & Research

Exa is a search engine built for AI agents. It indexes the web with neural retrieval, so queries can be written as natural-language descriptions of the ideal page rather than keyword strings. This power gives Kiro agents a unified surface for:

- **Semantic web search** over the live web, with optional filters by domain, date, and category (company / people)
- **Clean content extraction** from any URL as markdown
- **Code and documentation search** tuned for programming questions
- **Multi-query research** across diverse angles when a single search isn't enough

Use this power any time your agent needs information that is not already in the repo, the knowledge cutoff, or another connected MCP server.

## Available MCP Server

This power activates a single remote MCP server: **`https://mcp.exa.ai/mcp`**.

Tools provided:

| Tool | Status | Purpose |
|---|---|---|
| `web_search_exa` | Primary | Natural-language web search; returns clean text excerpts from top results |
| `web_fetch_exa` | Primary | Fetch any URL as clean markdown; batch multiple URLs in one call |
| `web_search_advanced_exa` | Primary | Search with filters (domain allow/deny, date range, category, location, etc.) |

## Onboarding

### 1. Get an API key

Sign up at [dashboard.exa.ai](https://dashboard.exa.ai) and create an API key. New accounts include free credits; see [exa.ai/pricing](https://exa.ai/pricing) for paid tiers.

### 2. Set the environment variable

Export your key as `EXA_API_KEY` in the shell Kiro launches from:

```bash
export EXA_API_KEY="your-key-here"
```

Or add it to Kiro's MCP environment config. The `mcp.json` in this power passes the key through the `x-api-key` header.

### 3. Start using it

Kiro activates the Exa power automatically when your prompt matches one of the trigger keywords (`search`, `research`, `find`, `look up`, `crawl`, etc.). You can also name Exa explicitly (e.g. "use Exa to find..."). Once activated, this POWER.md loads and the MCP tools become available.

## Choosing the Right Tool

Exa's default search type is `auto`, which routes between neural and keyword methods and returns in under a second. Pick based on what the user is actually asking for:

| If the query sounds like... | Use |
|---|---|
| `what is`, `who is`, `when did`, quick factual lookup | `web_search_exa` (auto) |
| `find me companies / people / papers that...`, single-shot discovery | `web_search_exa` with `category:company` / `category:people`, or `web_search_advanced_exa` for richer filters |
| `research`, `deep dive`, `competitive analysis`, `everything about`, multi-constraint synthesis | Multiple targeted `web_search_exa` / `web_search_advanced_exa` queries across different angles, then synthesize |
| `how do I use`, `docs for`, `example of`, any programming question | `web_search_exa` with a specific, descriptive query (e.g. "Next.js 15 partial prerendering app router example") |
| User pasted a URL or you already have one from context | `web_fetch_exa` |

**Escalation rule:** if a query has 3+ constraints or requires synthesis across multiple sources, decompose it into several queries rather than trying one broad `web_search_exa`. Run 3-5 angles in parallel, deduplicate, then synthesize; see `steering/synthesis.md`.

## Steering Files

Load the steering file(s) that match what the agent is actually doing. Most tasks use 1-3; don't pre-load everything.

| Task | Steering file |
|---|---|
| Writing a good Exa query | `steering/searching.md` |
| Reading URLs for full content | `steering/extraction.md` |
| Filtering results against criteria (hard + soft filters) | `steering/filtering.md` |
| Synthesizing findings from many sources into prose | `steering/synthesis.md` |
| Evaluating source credibility (best-of, expert-finding) | `steering/source-quality.md` |
| Code, APIs, docs, error messages | `steering/patterns-code.md` |
| Finding or enriching companies | `steering/patterns-companies.md` |
| Finding or enriching people | `steering/patterns-people.md` |
| Recent news, launches, reactions | `steering/patterns-news.md` |
| Academic / research papers | `steering/patterns-papers.md` |
| Hidden connections (clients, collaborators, customers) | `steering/patterns-relationships.md` |

## Troubleshooting

**`401 Unauthorized` or `403`**
Your `EXA_API_KEY` is missing, expired, or wrong. Check the dashboard at [dashboard.exa.ai](https://dashboard.exa.ai) and confirm the env var is exported in the shell Kiro spawned from.

**`429 Too Many Requests`**
You hit a rate limit or ran out of credits. Check usage in the dashboard; upgrade plan or wait for the window to reset.

**Empty or low-quality results**
The query is probably too keyword-like. Rewrite it as a natural-language description of the ideal page. Add a category filter (`category:company`, `category:people`) if you want structured results.

**Tool not found**
Confirm the Exa power is activated and `mcp.exa.ai` is reachable from your network. Corporate proxies sometimes block MCP endpoints.

## Configuration

| Variable | Required | Purpose |
|---|---|---|
| `EXA_API_KEY` | Yes | Passed as `x-api-key` header to `mcp.exa.ai` |

No local install required; the MCP server is hosted by Exa.

## Links

- Exa homepage: [exa.ai](https://exa.ai)
- API docs: [docs.exa.ai](https://docs.exa.ai)
- Dashboard and keys: [dashboard.exa.ai](https://dashboard.exa.ai)
- MCP server source: [github.com/exa-labs/exa-mcp-server](https://github.com/exa-labs/exa-mcp-server)
