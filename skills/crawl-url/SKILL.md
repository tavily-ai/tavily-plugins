---
name: crawl-url
description: "Crawl any website and save pages as local markdown files. Use when you need to download documentation, knowledge bases, or web content for offline access or analysis. No code required - just provide a URL."
---

# URL Crawler

Crawls websites using Tavily Crawl API and saves each page as a separate markdown file.

## Prerequisites

**Tavily API Key Required** - Get your key at https://tavily.com

Add to `~/.claude/settings.json`:
```json
{
  "env": {
    "TAVILY_API_KEY": "tvly-your-api-key-here"
  }
}
```

## When to Use

- Crawl and extract content from a website
- Download API documentation, framework docs, or knowledge bases
- Save web content locally for offline access or analysis

## Quick Start

### Basic Crawl

```bash
curl -s -X POST https://api.tavily.com/crawl \
  -H "Authorization: Bearer $TAVILY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://docs.example.com",
    "max_depth": 2,
    "limit": 50
  }'
```

### With Instructions

```bash
curl -s -X POST https://api.tavily.com/crawl \
  -H "Authorization: Bearer $TAVILY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://react.dev",
    "instructions": "Focus on API reference pages and hooks documentation",
    "max_depth": 2,
    "limit": 50
  }'
```

## API Reference

### Endpoint

```
POST https://api.tavily.com/crawl
```

### Headers

| Header | Value |
|--------|-------|
| `Authorization` | `Bearer <TAVILY_API_KEY>` |
| `Content-Type` | `application/json` |

### Request Body

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `url` | string | Required | The root URL to begin crawling |
| `instructions` | string | null | Natural language guidance for the crawler |
| `max_depth` | integer | 1 | How far from base URL to explore (1-5) |
| `max_breadth` | integer | 20 | Max links to follow per page level |
| `limit` | integer | 50 | Total pages to crawl before stopping |
| `extract_depth` | string | `"basic"` | Extraction quality: `basic` or `advanced` |
| `timeout` | integer | 150 | Max seconds to wait (10-150) |

### Response Format

```json
{
  "base_url": "https://docs.example.com",
  "results": [
    {
      "url": "https://docs.example.com/page",
      "raw_content": "# Page Title\n\nMarkdown content..."
    }
  ],
  "response_time": 2.5,
  "request_id": "abc-123"
}
```

- **results**: Array of crawled pages with `url` and `raw_content`
- **raw_content**: Extracted content in markdown format

## Saving Results to Markdown Files

The curl command returns JSON. To save each page as a separate markdown file:

```bash
# 1. Crawl and save response to temp file
curl -s -X POST https://api.tavily.com/crawl \
  -H "Authorization: Bearer $TAVILY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://docs.example.com",
    "max_depth": 2,
    "max_breadth": 50,
    "limit": 50
  }' > /tmp/crawl_results.json

# 2. Create output directory
mkdir -p ./crawled_docs

# 3. Save each result as a markdown file (handles control characters)
count=$(jq '.results | length' /tmp/crawl_results.json)
for i in $(seq 0 $((count - 1))); do
  url=$(jq -r ".results[$i].url" /tmp/crawl_results.json)
  # Create safe filename (remove protocol, convert special chars to underscores)
  filename=$(echo "$url" | sed -E 's|https?://||; s|[^a-zA-Z0-9/_-]|_|g; s|/|_|g; s|_+|_|g; s|^_||; s|_$||' | cut -c1-100)
  filename="${filename:-page_$i}.md"
  # Write frontmatter and content directly with jq (avoids control character issues)
  {
    echo "---"
    echo "source_url: $url"
    echo "---"
    echo ""
    jq -r ".results[$i].raw_content" /tmp/crawl_results.json
  } > "./crawled_docs/$filename"
  echo "Saved: $filename"
done
echo "Total: $count pages saved to ./crawled_docs/"
```

This saves each crawled page as a separate `.md` file with frontmatter containing the source URL.

## Examples

### Crawl Documentation Site

```bash
curl -s -X POST https://api.tavily.com/crawl \
  -H "Authorization: Bearer $TAVILY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://docs.anthropic.com",
    "max_depth": 2,
    "max_breadth": 50,
    "limit": 100
  }'
```

### Focused Crawl with Instructions

```bash
curl -s -X POST https://api.tavily.com/crawl \
  -H "Authorization: Bearer $TAVILY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://nextjs.org/docs",
    "instructions": "Focus on App Router documentation and API routes",
    "max_depth": 3,
    "limit": 50
  }'
```

### Quick Single-Page Extract

```bash
curl -s -X POST https://api.tavily.com/crawl \
  -H "Authorization: Bearer $TAVILY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://docs.stripe.com/api",
    "max_depth": 1,
    "limit": 10
  }'
```

## Notes

- **Crawl Time**: Deeper crawls take longer (depth 3+ may take minutes)
- **Instructions**: Use natural language to guide the crawler toward specific content
- **Limit**: Set appropriate limits to avoid excessive API usage
