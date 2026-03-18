---
name: coingecko-categories
description: Track performance of crypto sectors and categories (DeFi, Layer 1, Gaming, AI, etc.) using the CoinGecko free API. Shows market cap, volume, and 24h price change by category. Free tier, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# CoinGecko Crypto Categories

Monitor the performance of crypto sectors — DeFi, Layer 1, Layer 2, AI tokens, gaming, meme coins, and more.

**Base URL:** `https://api.coingecko.com/api/v3`
**Authentication:** None required (free public API).

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/coins/categories` (GET) | All 200+ crypto categories with market cap, volume, and 24h change | None | order | No |

## Parameters

- **order**: Sort results by `market_cap_desc` (default), `market_cap_asc`, `name_desc`, `name_asc`, `market_cap_change_24h_desc`, `market_cap_change_24h_asc`

## Response Fields (per category)

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Category ID (e.g. `decentralized-finance-defi`) |
| `name` | string | Category display name |
| `market_cap` | float | Total market cap of coins in category (USD) |
| `market_cap_change_24h` | float | 24h market cap change % |
| `volume_24h` | float | 24h trading volume (USD) |
| `updated_at` | string | Last update timestamp |

## Notes

- Rate limit: ~5–15 requests/minute on free tier.
- Covers 200+ categories including DeFi, L1, L2, AI, RWA, gaming, and meme coins.
- Sort by `market_cap_change_24h_desc` to find today's leading sectors.
