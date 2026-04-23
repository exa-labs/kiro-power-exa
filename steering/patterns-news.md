# Query Patterns: News and Recent Events

```
web_search_exa { "query": "category:news [topic] announcement", "numResults": 10 }
web_search_exa { "query": "[topic] news update latest development [month year]", "numResults": 10 }
```

For reactions and sentiment on recent events, search across platforms and angles:

```
web_search_exa { "query": "[event] reaction analysis commentary", "numResults": 10 }
web_search_exa { "query": "[event] criticism concerns issues bugs", "numResults": 10 }
```

## Narrowing by Date

When the task specifies a recent window, use `web_search_advanced_exa` with date filters rather than leaving it to semantic matching:

```
web_search_advanced_exa {
  "query": "launch announcement [topic]",
  "category": "news",
  "startPublishedDate": "<YYYY-MM-DD, calculated from today>",
  "endPublishedDate": "<YYYY-MM-DD, today>",
  "numResults": 10
}
```

Always calculate the dates from today; never copy the placeholders above verbatim.
