---
name: deal-hunt
description: "Find the best current deals/coupons for a specific product. Searches web for deals and returns raw results for analysis."
---

# Deal Hunt

Search for deals on any product using Tavily Research API. Returns comprehensive deal research for Claude to analyze.

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

## Quick Start

### Basic Deal Search

```bash
curl -N -X POST https://api.tavily.com/research \
  -H "Authorization: Bearer $TAVILY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "input": "Find the best deals, coupon codes, and discounts for Sony WH-1000XM5 headphones. Search for: 1) Current sale prices and deals 2) Active coupon codes and promo codes 3) Discount offers and bundle deals",
    "model": "mini",
    "stream": true,
    "citation_format": "numbered"
  }'
```

### With Specific Retailers

```bash
curl -N -X POST https://api.tavily.com/research \
  -H "Authorization: Bearer $TAVILY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "input": "Find the best deals, coupon codes, and discounts for MacBook Air M3 at Amazon, Best Buy, and Walmart. Search for: 1) Current sale prices and deals 2) Active coupon codes and promo codes 3) Discount offers and bundle deals",
    "model": "mini",
    "stream": true,
    "citation_format": "numbered"
  }'
```

### Gaming Console Example

```bash
curl -N -X POST https://api.tavily.com/research \
  -H "Authorization: Bearer $TAVILY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "input": "Find the best deals, coupon codes, and discounts for PS5 console. Search for: 1) Current sale prices and deals 2) Active coupon codes and promo codes 3) Discount offers and bundle deals",
    "model": "mini",
    "stream": true,
    "citation_format": "numbered"
  }'
```

## API Reference

### Endpoint

```
POST https://api.tavily.com/research
```

### Headers

| Header | Value |
|--------|-------|
| `Authorization` | `Bearer <TAVILY_API_KEY>` |
| `Content-Type` | `application/json` |

### Request Body

| Field | Type | Value | Description |
|-------|------|-------|-------------|
| `input` | string | Required | Research prompt including product and deal queries |
| `model` | string | `"mini"` | Always use mini for deal searches |
| `stream` | boolean | `true` | Stream results (single call, waits for completion) |
| `citation_format` | string | `"numbered"` | Citation format for sources |

### Input Prompt Template

Always structure the input prompt to cover all deal types:

```
Find the best deals, coupon codes, and discounts for {PRODUCT}. Search for:
1) Current sale prices and deals
2) Active coupon codes and promo codes
3) Discount offers and bundle deals
```

### Response Format

Returns Server-Sent Events (SSE) with research content and sources:

```
event: chat.completion.chunk
data: {"choices":[{"delta":{"content":"# Deal Research Results\n\n..."}}]}

event: research.sources
data: {"sources":[{"url":"https://...","title":"..."}]}

event: done
data: {"response_time":30.5}
```

## Output Schema for Analysis

After getting research results, Claude should structure findings as:

```json
{
  "product": "Sony WH-1000XM5",
  "best_deal": {
    "price": 279.99,
    "original_price": 399.99,
    "discount": "30% off",
    "retailer": "Amazon",
    "url": "https://...",
    "condition": "new",
    "in_stock": true
  },
  "all_deals": [
    {
      "price": 279.99,
      "retailer": "Amazon",
      "url": "https://...",
      "notes": "Prime shipping"
    }
  ],
  "coupons": [
    {
      "code": "AUDIO10",
      "discount": "10% off",
      "retailer": "Best Buy",
      "expires": "2026-01-31"
    }
  ],
  "summary": "Best new price is $279.99 at Amazon (30% off)."
}
```

Claude extracts prices from content, compares deals, and presents the best options with purchase links.
