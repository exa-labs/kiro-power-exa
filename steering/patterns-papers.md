# Query Patterns: Research Papers

Use `category:research paper` for Exa's paper index.

```
// By topic
web_search_exa { "query": "category:research paper sparse attention mechanisms for long context transformers", "numResults": 10 }

// Survey/review papers
web_search_exa { "query": "category:research paper [topic] comprehensive survey review", "numResults": 10 }

// By author
web_search_exa { "query": "category:research paper [author name] [topic]", "numResults": 5 }

// By recency (encode time in query)
web_search_exa { "query": "category:research paper large language model advances 2025 2026", "numResults": 10 }
```

To find seminal papers: search for survey papers first, then deep-read them with `web_fetch_exa` to extract foundational references.

## Scoping to Venues

For papers from specific venues, layer `web_search_advanced_exa` with domain filters:

```
web_search_advanced_exa {
  "query": "sparse attention mechanisms long context",
  "category": "research paper",
  "includeDomains": ["arxiv.org", "openreview.net"],
  "numResults": 10
}
```
