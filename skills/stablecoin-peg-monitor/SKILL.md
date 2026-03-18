---
name: stablecoin-peg-monitor
description: Track stablecoin circulating supply, chain distribution, and peg status using the DefiLlama Stablecoins API. Free tier, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# Stablecoin Supply & Peg Monitor

Track stablecoin circulating supply, chain distribution, and peg deviation using the DefiLlama Stablecoins API.

**Base URL:** `https://stablecoins.llama.fi`
**Authentication:** None required.

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/stablecoins` (GET) | All tracked stablecoins with circulating supply and current price | None | includePrices | No |
| `/stablecoin/{id}` (GET) | Detailed chain breakdown and supply history for a single stablecoin | id (path) | None | No |
| `/stablecoinchains` (GET) | Stablecoin supply aggregated by blockchain | None | None | No |

## Parameters

- **includePrices**: `true` to include current peg price for de-peg detection
- **id**: Stablecoin ID integer from the `id` field in `/stablecoins`

## Response Fields (per stablecoin, `/stablecoins`)

| Field | Type | Description |
|-------|------|-------------|
| `id` | integer | Stablecoin ID (use in `/stablecoin/{id}`) |
| `name` | string | Stablecoin name (e.g. `Tether`) |
| `symbol` | string | Ticker symbol (e.g. `USDT`) |
| `pegType` | string | Peg type (e.g. `peggedUSD`, `peggedEUR`) |
| `pegMechanism` | string | Mechanism: `fiat-backed`, `crypto-backed`, `algorithmic` |
| `circulating` | object | Circulating supply (pegged USD value) |
| `price` | float | Current price (with `includePrices=true`) |
| `chains` | string[] | Chains where the stablecoin is deployed |

## Notes

- Use `price` vs `1.0` to detect de-peg events (deviation > 0.5% is notable).
- `/stablecoinchains` shows total stablecoin liquidity per blockchain.
