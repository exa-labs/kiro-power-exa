# Searching with Exa

Three search tools are available to a Kiro agent:

- **`web_search_exa`**: search by natural-language query. Supports `query` and `numResults`. Use `category:<type>` inline in the query string for category filtering.
- **`web_search_advanced_exa`**: same backend with explicit filters (`includeDomains`, `excludeDomains`, `startPublishedDate`/`endPublishedDate`, `category`, `userLocation`) and a `type` selector (`auto` — the default, works with all filters — plus `fast` and `instant` for lower latency), along with configurable summaries, highlights, and subpage crawling. Reach for this when you need domain, date, or text constraints that `category:` alone can't express. Its `category` accepts a wider set than the inline form: `company`, `people`, `research paper`, `news`, `personal site`, `financial report`, `pdf`, `github`.
- **`web_fetch_exa`**: read full content from known URLs. Use after search when snippets are insufficient. See `extraction.md` in this directory.

For filtering extracted results against task criteria, see `filtering.md`.

## How Exa Search Works

Exa uses vector embeddings, not keywords. It finds pages semantically similar to your query. It does not match keywords exactly, does not understand boolean logic (AND/OR/NOT), and does not validate that results meet your criteria. You are describing a target page, and Exa returns the nearest neighbors in embedding space.

## Writing Good Queries

**Describe the page you want to find**, not the fact you want to know.

| Looking for | Bad query | Good query |
|---|---|---|
| Blog posts about X | "X" | "detailed blog post about X written by a practitioner" |
| Company doing Y | "Y company" | "category:company startup building Y for enterprise" |
| Person at company | "person at company" | "category:people senior engineer at Acme" |

Write queries as natural grammatical phrases.

**`numResults` sizing; match to query precision:**

| Query precision | numResults | Example |
|---|---|---|
| Named entity (specific person/company) | 3-5 | `"WaveForms AI founding story funding details"` |
| Precise filter (narrow category + constraints) | 5-8 | `"category:company developer tools API testing Series A"` |
| Broad discovery (wide category, few constraints) | 10 | `"category:news engineer launches startup 2025 2026"` |

Cap `numResults` at 10. If you need more coverage, run additional queries with different angles rather than pushing a single query higher; larger result sets return diminishing signal and more noise.

**Use category filters** when searching for a specific entity type. Inline categories available to `web_search_exa`: `company`, `research paper`, `news`, `personal site`, `people`. Add `category:<type>` at the start of the query string.

```
web_search_exa { "query": "category:research paper sparse attention mechanisms for long context", "numResults": 10 }
web_search_exa { "query": "category:people VP Engineering AI infrastructure San Francisco", "numResults": 10 }
web_search_exa { "query": "category:company developer tools for API testing", "numResults": 10 }
```

For stricter constraints (domain allow/deny, explicit date windows, geo-targeting), use `web_search_advanced_exa`:

```
web_search_advanced_exa {
  "query": "partial prerendering app router examples",
  "includeDomains": ["nextjs.org", "vercel.com"],
  "startPublishedDate": "<YYYY-MM-DD, calculated from today>",
  "numResults": 10
}
```

## Category Filter Restrictions

The `company` and `people` categories reject most filters — passing an unsupported filter returns `400 Invalid request body`:

- `company`: no date filters (`startPublishedDate` / `endPublishedDate`). `excludeDomains` and `userLocation` are allowed.
- `people`: no date/crawl-date filters and no `excludeDomains`; `includeDomains` only accepts LinkedIn domains. Push role, company, seniority, and location constraints into the query text instead.

Other categories (`news`, `research paper`, `personal site`, `financial report`) accept domain and date filters normally.
company, research paper, news, personal site, financial report, people 

## Query Diversity

When running multiple queries on the same topic, target genuinely different angles, not synonym swaps. "overhyped" vs "overrated" vs "disappointment" are the same angle. A skeptic angle vs a builder angle vs a practitioner angle are genuinely different.

**Word order affects embeddings.** "Python async patterns for web scraping" and "web scraping async patterns in Python" can return different results. Use this when you need coverage: run 2-3 phrasings.

## Encoding Time

If your task involves time ("last week", "recent", "this month"), calculate exact dates FIRST from the current date in your environment context. Then either encode dates semantically in the query ("published in March 2026") or pass them to `web_search_advanced_exa` as `startPublishedDate` / `endPublishedDate`. Never eyeball dates, and never reuse dates from examples in this file.

## Anti-Patterns

- Boolean operators ("AND", "NOT") are just words to Exa, not operators
- Quotes don't force exact phrase matching
- Very short queries (1-2 words) produce scattered, low-quality results
- Don't use dates from examples; always calculate from the current date

## When Searches Return Nothing

If a query returns 0 or only irrelevant results:

1. Make the query longer and more specific
2. Try a different angle, not a synonym swap
3. If multiple angles return nothing, the topic likely has limited web coverage; report that rather than fabricating results

## Domain-Specific Patterns

If your task involves any of these domains, load the relevant pattern file(s) for specialized query strategies. Most tasks use 1-2.

| File | Domain |
|---|---|
| `patterns-people.md` | People by role, company, location |
| `patterns-companies.md` | Companies by category, stage, competitors, funding |
| `patterns-papers.md` | Academic/research papers |
| `patterns-relationships.md` | Hidden connections (clients, collaborators, customers) |
| `patterns-code.md` | Code, APIs, docs, errors |
| `patterns-news.md` | News, recent events, reactions |

## When `web_fetch_exa` Fails

Fall back to any other fetch tool Kiro has available. If that also fails, skip it and work with remaining sources.

## After Getting Results

Exa returns similarity, not validation. Review titles and snippets, discard irrelevant results using judgment, and don't assume all results match your criteria. For the most promising results, use `web_fetch_exa` to read the full content:

```
web_fetch_exa {
  "urls": ["https://promising-url-1.com", "https://promising-url-2.com"]
}
```
