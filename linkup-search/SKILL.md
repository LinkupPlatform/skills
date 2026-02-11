---
name: linkup-search
description: "Guide for using the Linkup agentic web search API. Use when searching the web, scraping webpages, researching companies, extracting structured data from websites, or fetching webpage content via Linkup tools. Covers depth selection (standard vs deep), query writing, output types, the fetch endpoint, domain filtering and prioritization, and proven prompt patterns for business intelligence."
---

# Linkup Search — Agent Instructions

Linkup is an agentic web search API. You instruct it what to retrieve. You do the reasoning.

---

## Depth Selection

**The one question:** Does step N's output need to feed into step N+1?

- **No → `standard`** (€0.005)
- **Yes → `deep`** (€0.05)

**Rule of thumb:** One Google search → standard. Multiple tabs → deep.

### Standard (`depth="standard"`)

- Single iteration of retrieval
- Can split into parallel sub-searches **only if** explicitly instructed or required
- Can scrape **one** provided URL and run searches simultaneously
- Cannot reuse outputs between steps (no chaining)

### Deep (`depth="deep"`)

- Up to **10 iterations** of retrieval
- Each iteration aware of previous context
- Supports **sequential instructions**: search → find URL → scrape URL → extract
- Will refine or expand queries if information is missing

### When deep is required

Deep is for **chained sequential operations** where each step depends on the last:

- Search → find URLs → scrape those URLs
- Find a LinkedIn profile → scrape it → extract data
- Find a pricing page → scrape it → extract plan details
- Research across multiple pages with URL discovery

---

## Query Writing

### Core principle

Focus on **data retrieval**, not answer generation. You do the analysis.

### Query styles

- **Simple lookups** → keywords: `"NVIDIA Q4 2024 revenue"`
- **Complex extraction** → natural language instructions: what to find, where to look, what to extract
- **Broad research** → `"Run several searches with adjacent keywords"`
- **Scraping** → include the URL and say what to extract

### The four components of a strong prompt

| Component | Purpose | Example |
|-----------|---------|---------|
| **Goal** | What to find | "Find data to estimate TCO of Total SA's intranet" |
| **Scope** | Where to look | "Analyze homepage, about page, and blog" |
| **Criteria** | What to extract | "Products, business model, target market" |
| **Format** | How to return it | "Return as bullet points with sources" |

### When simple prompts are enough

For **breadth** (source coverage) over precision, keep it simple:

- `"Find latest research on consciousness. Run several searches with adjacent keywords."`
- `"Find recent news about OpenAI. Run several searches with adjacent keywords."`

Use structured prompts when you need specific data points, page scraping, or defined output formats.

### Bad vs good queries

| Bad | Good (standard) | Good (deep) |
|-----|-----------------|-------------|
| Tell me about the company | Scrape {url}. Extract what it does, who it serves, main differentiator. | Find homepage, product page, about page, LinkedIn. Scrape each. Return: what it does, products, target customers, value proposition. |
| Analyze GTM motion | Search if {company} uses self-service signup, free trials, or demos. Scrape {url}. Return PLG or SLG with evidence. | Find and scrape homepage, pricing page, signup flow. Extract: self-service options, free trial, demo CTAs, pricing transparency. Determine PLG vs SLG. |

---

## Output Types

| Type | Use case | Notes |
|------|----------|-------|
| `searchResults` | LLM grounding, agent consumption | Raw source chunks. Default for agents. |
| `sourcedAnswer` | User-facing Q&A | Answer with citations. Set `includeInlineCitations: true` for inline refs. |
| `structured` | Pipelines, data extraction | JSON matching a schema via `structuredOutputSchema` (root must be type `object`). Set `includeSources: true` to get sources alongside. |

---

## Domain Filtering and Prioritization

### Include / Exclude

- `includeDomains`: Only search these domains (up to 100)
- `excludeDomains`: Never search these domains (up to 50)
- If both used, results only include URLs matching inclusion

### Prioritize with `<guidance>` tags

Use XML structure **inside the query string** to prioritize specific sources:

```
<guidance>
Use only the sources listed here. Do not rely on any external references.
  - wikipedia.org
  - lvmh.com
<priority>
  - wikipedia.org
</priority>
</guidance>
Who is the CEO of LVMH?
```

---

## Fetch Endpoint

Use `/fetch` instead of `/search` when you have one known URL and just need its content as markdown.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `url` | string (URI) | ✓ | URL to fetch |
| `renderJs` | boolean | No (default: false) | Render JavaScript |
| `includeRawHtml` | boolean | No | Include raw HTML in response |
| `extractImages` | boolean | No | Extract images from page |

**Pricing:**

| renderJs | Cost | When to use |
|----------|------|-------------|
| `false` | €0.001 | Static pages, blogs, documentation |
| `true` | €0.005 | SPAs, JS-rendered content, dynamic pages |

Default to `renderJs: false`. Only use `true` when content requires JavaScript rendering.

**Response:** Returns `markdown` (string), optionally `rawHtml` (string) and `images` (array of `{url, alt}`).

---

## Parameters Reference

### /search

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `q` | string | ✓ | The search query |
| `depth` | `"standard"` \| `"deep"` | ✓ | |
| `outputType` | `"searchResults"` \| `"sourcedAnswer"` \| `"structured"` | ✓ | |
| `structuredOutputSchema` | JSON string | When structured | Root must be type `object` |
| `includeSources` | boolean | No | For structured output only. Modifies response schema. |
| `includeInlineCitations` | boolean | No | For sourcedAnswer only |
| `includeImages` | boolean | No | Include images in results |
| `fromDate` | YYYY-MM-DD | No | Filter results from this date |
| `toDate` | YYYY-MM-DD | No | Filter results until this date |
| `includeDomains` | string[] | No | Up to 100 domains |
| `excludeDomains` | string[] | No | Up to 50 domains |
| `maxResults` | number | No | Limit number of results returned |

---

## Prompt Templates

### Company overview (deep)

```
You are an expert business analyst. Generate a detailed description of {company}.
Consult: homepage, product page, about page, LinkedIn profile.
First find these page URLs, then scrape each. Do not stop until all scraped.
Return: what it does, products/services, target customers, value proposition, founding story.
```

### ICP extraction (deep)

```
Determine {company}'s Ideal Customer Profile.
Find and scrape: homepage, use case pages, 2-3 recent blog posts.
Extract: industries mentioned, company sizes, job titles targeted, pain points addressed.
Also extract visible customer logos.
Infer: industry, company size, buyer persona, key pain points.
```

### Value proposition mapping (deep)

```
Map {company}'s value proposition from {website}.
Find and scrape homepage and primary product pages.
Extract: headline claims, customer benefits, differentiator language, CTAs.
Synthesize into value proposition summary. Focus on concrete claims, not marketing fluff.
```

### GTM motion analysis (deep)

```
Determine if {company} follows PLG or SLG.
Find and scrape: homepage, pricing page, signup flow.
Extract: self-service signup options, free trial availability, demo CTAs,
  sales contact requirements, pricing transparency.
Conclude PLG or SLG with evidence.
```

### LinkedIn research (deep, sequential)

```
Find LinkedIn posts on {topic}.
For each URL, extract the LinkedIn comments.
Return the LinkedIn profile URL of each commenter.
```

### Simple research (standard)

```
Find {company}'s latest funding round amount and date.
```

### Scrape + search in parallel (standard)

```
Scrape {url} and extract what the company does and its services.
Also search for "{company name}" to find directory or registry listings.
```

---

## Errors

| Code | Meaning |
|------|---------|
| 400 | Missing/invalid parameter **or** no results found |
| 401 | Invalid API key |
| 403 | No permission |
| 409 | Resource conflict |
| 429 | Out of credit **or** rate limited |
| 500 | Linkup server error |

**No credit is deducted on any error**, including when no results are found.

**Rate limit:** 10 queries per second per organization.

**SDK error types:** `LinkupInvalidRequestError`, `LinkupNoResultError`, `LinkupAuthenticationError`, `LinkupInsufficientCreditError`, `LinkupTooManyRequestsError`, `LinkupUnknownError`.

---

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Starting with deep when standard works | Start standard. Escalate if results are thin. |
| Vague query: "Tell me about the company" | Specify what you need: revenue? product? team? |
| Asking standard to find URLs then scrape them | Use deep — this requires sequential chaining |
| Not including a URL you already have | Include it and say "scrape this page" |
| Asking Linkup to analyze or reason | Ask for data. You do the analysis. |
| Using `renderJs: true` for static pages | Default to `false`. Only `true` for JS-rendered SPAs. |
| Ignoring that 400 can mean no results | Handle gracefully. Retry with broader query. |
| Firing >10 parallel calls | Rate limit is 10 qps/org. Stagger if needed. |

---

## Pricing

| Endpoint | Variant | Cost |
|----------|---------|------|
| /search | standard | €0.005 |
| /search | deep | €0.05 |
| /fetch | renderJs: false | €0.001 |
| /fetch | renderJs: true | €0.005 |

**Cost optimization:** 3–5 parallel standard calls with focused sub-queries (€0.015–0.025) is often faster and cheaper than one deep call (€0.05). Use when sub-queries are independent.

---

## MCP Setup

Two tools: `linkup-search` (query, depth) and `linkup-fetch` (url, renderJs).

| Client | Setup |
|--------|-------|
| **VS Code / Cursor** | Add to MCP config: `{"servers":{"linkup":{"url":"https://mcp.linkup.so/mcp?apiKey=YOUR_API_KEY","type":"http"}}}` |
| **Claude Code** | `claude mcp add --transport http linkup https://mcp.linkup.so/mcp?apiKey=YOUR_API_KEY` |
| **Claude Desktop** | Download [MCPB bundle](https://github.com/LinkupPlatform/linkup-mcp-server/releases/latest/download/linkup-mcp-server.mcpb), double-click to install |

Auth format (v2.x): `apiKey=YOUR_API_KEY` in args. Old v1.x `env` format no longer works.

---

## Quick Reference

```
STANDARD (€0.005): parallel sub-searches ✓  scrape one provided URL ✓  sequential chains ✗
DEEP (€0.05):      up to 10 iterations ✓   sequential chains ✓       multi-URL scraping ✓
FETCH:             one known URL → €0.001 (no JS) or €0.005 (with JS)
RATE LIMIT:        10 queries/second per organization
OUTPUT:            searchResults (default) | sourcedAnswer | structured
DEPTH DECISION:    Does step N feed into step N+1? Yes → deep. No → standard.
QUERIES:           Keywords for facts. Instructions for extraction. Be specific.
COVERAGE:          "Run several searches with adjacent keywords" (works in standard)
ERRORS:            No credit deducted on errors. 400 can mean no results. 429 = credit or rate limit.
DOMAIN PRIORITY:   Use <guidance> + <priority> XML tags in query string
MCP TOOLS:         linkup-search (query, depth) + linkup-fetch (url, renderJs)
MCP REMOTE:        https://mcp.linkup.so/mcp?apiKey=YOUR_API_KEY
```
