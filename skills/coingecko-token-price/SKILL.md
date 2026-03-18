---
name: coingecko-token-price
description: Look up real-time price, market cap, trading volume, and 24h change for any cryptocurrency using the CoinGecko free API. Supports multiple currencies. Free tier, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# CoinGecko Token Price & Market Data

Query real-time price, market cap, 24h volume, and price change for any token by its CoinGecko ID.

**Base URL:** `https://api.coingecko.com/api/v3`
**Authentication:** None required (free public API).

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/simple/price` (GET) | Real-time price for one or more tokens in target currencies | ids, vs_currencies | include_market_cap, include_24hr_vol, include_24hr_change | No |
| `/coins/list` (GET) | Full list of all ~15,000 coins with IDs and symbols (for ID lookup) | None | None | No |

## Parameters

- **ids**: Comma-separated CoinGecko coin IDs (e.g. `bitcoin,ethereum,solana`)
- **vs_currencies**: Comma-separated target currencies (e.g. `usd,btc,eth`)
- **include_market_cap**: `true` to include market cap
- **include_24hr_vol**: `true` to include 24h volume
- **include_24hr_change**: `true` to include 24h price change %

## Common Coin IDs

| Token | CoinGecko ID |
|-------|-------------|
| Bitcoin | `bitcoin` |
| Ethereum | `ethereum` |
| Solana | `solana` |
| BNB | `binancecoin` |
| XRP | `ripple` |
| Dogecoin | `dogecoin` |
| Avalanche | `avalanche-2` |
| Chainlink | `chainlink` |

## Notes

- Rate limit: ~5–15 requests/minute on free tier (no key required).
- Prices update approximately every 60 seconds.
