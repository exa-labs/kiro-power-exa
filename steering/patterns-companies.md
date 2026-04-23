# Query Patterns: Companies

Use `category:company` for structured company data (funding, headcount, description).

```
// By category
web_search_exa { "query": "category:company AI infrastructure startups San Francisco", "numResults": 10 }

// By stage
web_search_exa { "query": "category:company Series B fintech payments", "numResults": 10 }

// Similar to known company
web_search_exa { "query": "category:company companies like Stripe", "numResults": 8 }
```

For competitive intelligence, layer multiple angles:

```
web_search_exa { "query": "category:company companies like [target]", "numResults": 10 }
web_search_exa { "query": "category:company [category] software tools", "numResults": 10 }
web_search_exa { "query": "[category] startup launch funding announcement recently", "numResults": 10 }
```

For funding and investors:

```
web_search_exa { "query": "[company] funding round raised investors", "numResults": 5 }
web_search_exa { "query": "category:company [company]", "numResults": 5 }
```

## Narrower Constraints

For strict category + stage + date + geography filters, move to `web_search_advanced_exa`:

```
web_search_advanced_exa {
  "query": "developer tools for API testing",
  "category": "company",
  "startPublishedDate": "2024-01-01",
  "userLocation": "US",
  "numResults": 10
}
```

(Advanced search `numResults` also capped at 10; run multiple angles if you need broader coverage.)

## Deduplication

When enriching a list of companies across multiple queries, deduplicate by canonical URL (company homepage) first, then by name as a fallback.
