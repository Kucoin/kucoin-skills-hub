---
name: coingecko-trending
description: Fetch the top trending cryptocurrencies on CoinGecko ranked by 24h search volume. Also returns trending NFTs and categories. Free tier, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# CoinGecko Trending Coins

Fetch the top trending cryptocurrencies on CoinGecko — ranked by search volume in the last 24 hours.

**Base URL:** `https://api.coingecko.com/api/v3`
**Authentication:** None required (free public API).

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/search/trending` (GET) | Top 7 trending coins, top 5 trending NFTs, and trending categories by 24h search volume | None | None | No |

## Response Fields (per coin item)

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | CoinGecko coin ID |
| `name` | string | Coin full name |
| `symbol` | string | Ticker symbol |
| `market_cap_rank` | integer | Current market cap rank |
| `price_btc` | float | Price denominated in BTC |
| `score` | integer | Trending rank (0 = #1 trending) |

## Notes

- Rate limit: ~5–15 requests/minute on free tier.
- Trending list updates every few minutes.
- Returns top 7 coins, top 5 NFTs, and trending categories in one call.
