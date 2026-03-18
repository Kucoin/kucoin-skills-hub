---
name: wallet-token-holdings
description: Query real-time token prices and 24h market statistics using the Binance public API. Supports current spot prices, rolling 24h stats, and exchange info for portfolio valuation. No authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# Wallet Token Holdings

Query real-time token prices and market statistics using the Binance public API for portfolio valuation.

**Base URL:** `https://api.binance.com/api/v3`
**Authentication:** None required.

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/ticker/price` (GET) | Latest spot price for one or all trading pairs | None | symbol, symbols | No |
| `/ticker/24hr` (GET) | Rolling 24h price change stats (one or all symbols) | None | symbol | No |
| `/exchangeInfo` (GET) | Trading pair metadata including base/quote assets and status | None | symbol, symbols | No |

## Parameters

- **symbol**: Single trading pair (e.g. `BTCUSDT`)
- **symbols**: JSON array of pairs (e.g. `["BTCUSDT","ETHUSDT"]`)

## Response Fields (`/ticker/price`, per ticker)

| Field | Type | Description |
|-------|------|-------------|
| `symbol` | string | Trading pair (e.g. `BTCUSDT`) |
| `price` | string | Latest spot price |

## Response Fields (`/ticker/24hr`, per ticker)

| Field | Type | Description |
|-------|------|-------------|
| `symbol` | string | Trading pair |
| `lastPrice` | string | Latest price |
| `priceChangePercent` | string | 24h change % |
| `volume` | string | 24h volume (base currency) |
| `quoteVolume` | string | 24h volume (quote currency) |

## Notes

- Rate weight: `/ticker/price` = 2 (single) or 4 (all); `/ticker/24hr` = 2 (single) or 80 (all).
- Use `symbols` parameter to batch multiple lookups in one request (more efficient).
- `GET /exchangeInfo` without params returns all ~2,000 trading pairs with base/quote asset metadata.
