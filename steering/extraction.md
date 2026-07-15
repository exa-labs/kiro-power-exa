# Extracting Structured Data from Search Results

After running searches, you often need to extract structured information from the results. This file covers how to do that well.

## When You Have Enough from Snippets

Exa search results include titles, URLs, and text snippets (highlights). For many fields (company name, person name, funding round, publication date), the snippet is sufficient. Extract directly from what you have before fetching full pages.

## When to Deep-Read with `web_fetch_exa`

Fetch the full page when:

- The snippet mentions what you need but doesn't include the actual value
- You need to read body text to make a judgment call (e.g. "does this blog post show genuine design opinion or is it generic?")
- You need to extract multiple fields from a single rich source (case study page, team page, filing)
- The task requires reading beyond the first few sentences

```
web_fetch_exa {
  "urls": ["https://source-1.com", "https://source-2.com"]
}
```

Batch up to 5-10 URLs per fetch call to minimize round trips. `web_fetch_exa`'s `maxCharacters` defaults to 3000, which truncates longer pages after the top section — raise it (e.g. 8000-15000) whenever you need full page context rather than just the opening.

## Content Controls on `web_search_advanced_exa`

`web_search_exa` always returns highlights. On `web_search_advanced_exa`, pick ONE content mode per call rather than stacking them:

- **Highlights (default)** — `enableHighlights: true`. Best for agent workflows: query-relevant excerpts at low token cost. Let Exa pick the length; only set `highlightsMaxCharacters` for a fixed budget, and keep it above ~400 (smaller truncates too aggressively to be useful downstream).
- **Text** — set `textMaxCharacters` only when you need broad page context beyond excerpts; it costs more tokens than highlights.
- **Summary** — `enableSummary: true` only when you explicitly want Exa-side synthesis. It fires a per-result LLM call, so N results means N extra synthesis steps and added latency; use sparingly.

Stacking text + highlights bills for two views of the same page, and adding summary on top adds the per-result LLM cost. Default to highlights and escalate only when they're insufficient — then `web_fetch_exa` the best URLs for full text.

### Freshness

Control cache vs live-crawl with `maxAgeHours`:

- omit it — cache-first with live fallback (good default)
- small value (e.g. `24`) — for news, launches, and live market/policy updates
- `0` — always live-crawl (slowest; only when real-time freshness is essential)
- `-1` — cache-only (fastest; for latency-critical paths)

Set `livecrawlTimeout` (milliseconds) whenever a live crawl might be triggered so slow pages don't stall the call.

## Extracting into a Schema

When you've been given a schema (the "columns" for the result), extract each field per result:

1. **Structured fields** (name, date, URL, funding amount, ticker): extract the literal value. If not present, mark as missing rather than guessing.
2. **Categorical fields** (industry, stage, role level): map to the closest category. Note uncertainty if the mapping is ambiguous.
3. **Semantic fields** (sentiment, whether something qualifies as "genuine opinion", relevance to a theme): read the content and make a judgment call. Include a brief rationale so downstream synthesis can weigh your assessment.
4. **Negation fields** ("no review mentions X", "no Series A announcement"): these require checking that something is absent. Search for the positive case; if nothing surfaces, report absence with confidence level based on how thorough your coverage was.

## Handling Missing Data

- Mark fields as "not found" rather than guessing or leaving blank
- Distinguish "confirmed absent" (searched thoroughly, not there) from "not found" (didn't have access or coverage was limited)
- If a source is paywalled or inaccessible, note that explicitly

## Confidence Signals

When extracting, note the strength of the evidence:

- **Direct**: the source explicitly states the value (e.g. "We raised $20M in Series B")
- **Inferred**: the value is derived from context (e.g. headcount estimated from team page photos)
- **Uncertain**: single indirect signal, could be wrong

## Output Format

Return extracted data as compact structured output. For lists of entities:

```
[
  { "name": "...", "field_1": "...", "field_2": "...", "source": "url", "confidence": "direct" },
  ...
]
```

Or as a markdown table if that better suits the task. Keep output compact; verbosity compounds when results get merged across multiple searches.
