# Query Patterns: Code and Documentation

Always include programming language and framework/library in your query. Exa's index covers official docs, GitHub, Stack Overflow, and practitioner blog posts; specific queries unlock all of them.

```
// API usage
web_search_exa { "query": "Stripe API create subscription Node.js code example", "numResults": 5 }

// Error resolution
web_search_exa { "query": "React hydration mismatch server client explanation fix", "numResults": 10 }

// GitHub implementations
web_search_exa { "query": "GitHub repository [library] example project open source", "numResults": 10 }
```

For official docs on a service you already know by name, scope to the domain with `web_search_advanced_exa`:

```
web_search_advanced_exa {
  "query": "partial prerendering app router",
  "includeDomains": ["nextjs.org", "vercel.com"],
  "numResults": 8
}
```

Use `web_fetch_exa` to read official docs when you know the URL:

```
web_fetch_exa { "urls": ["https://nextjs.org/docs/app/api-reference/config/next-config-js/ppr"] }
```

## Tips

- Name the language, library, and version. "Python httpx retry on 429 with exponential backoff" beats "python http retry".
- For error messages, include the key phrase verbatim plus the framework name. "next.js 15 hydration mismatch server client" returns better than the error alone.
- When you need a runnable example, ask for it: "code example", "minimal reproducible example", "runnable snippet".
- For low-latency lookups, call `web_search_advanced_exa` with `"type": "fast"`; its `category` also supports `github` and `pdf` for scoping to repositories or PDF docs.
