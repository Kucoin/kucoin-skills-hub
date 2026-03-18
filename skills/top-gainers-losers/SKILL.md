---
name: top-gainers-losers
description: Identify the top gaining and losing tokens over the past 24 hours using the Binance public API. Filter by quote asset (USDT, BTC, ETH, BNB) and minimum volume to surface the biggest market movers. No authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# Top Gainers / Losers

Identify the top gaining and losing tokens over the past 24 hours using the Binance public API.

**Base URL:** `https://api.binance.com/api/v3`
**Authentication:** None required.

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/ticker/24hr` (GET) | Rolling 24h price change stats for all trading pairs (use to compute gainers/losers) | None | symbol | No |
| `/ticker/24hr?symbol={symbol}` (GET) | Rolling 24h stats for a single symbol | symbol | None | No |

## Parameters

- **symbol**: Trading pair (e.g. `BTCUSDT`). Omit to get all ~2,000 pairs.

## Response Fields (per ticker)

| Field | Type | Description |
|-------|------|-------------|
| `symbol` | string | Trading pair (e.g. `ETHUSDT`) |
| `priceChangePercent` | string | 24h price change % |
| `priceChange` | string | 24h price change in quote currency |
| `lastPrice` | string | Latest trade price |
| `volume` | string | 24h volume in base currency |
| `quoteVolume` | string | 24h volume in quote currency (USD equivalent for USDT pairs) |
| `highPrice` | string | 24h high price |
| `lowPrice` | string | 24h low price |
| `openPrice` | string | 24h open price |

## Workflow: Compute Top Gainers/Losers

1. `GET /ticker/24hr` — fetch all pairs
2. Filter by quote asset: keep symbols ending in `USDT`, `BTC`, `ETH`, or `BNB`
3. Filter by minimum `quoteVolume` to exclude low-liquidity tokens
4. Sort by `priceChangePercent` descending (gainers) or ascending (losers)

## Notes

- Full response (~2,000 pairs) is ~400KB. Cache for 30–60 seconds between requests.
- Rate weight: 2 for single symbol, 80 for all symbols (Binance weight limit: 1,200/min).
