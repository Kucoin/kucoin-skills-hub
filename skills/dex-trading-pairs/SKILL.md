---
name: dex-trading-pairs
description: Search and analyze DEX trading pairs, liquidity, volume, and price data across all chains using the DexScreener API. Free tier, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# DEX Trading Pairs & Volume

Search and analyze DEX trading pairs, liquidity, 24h volume, and real-time price data using the DexScreener API.

**Base URL:** `https://api.dexscreener.com`
**Authentication:** None required.

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/latest/dex/search` (GET) | Search pairs by token name, symbol, or contract address | q | None | No |
| `/latest/dex/tokens/{tokenAddress}` (GET) | Get all pairs for a token contract address | tokenAddress (path) | None | No |
| `/latest/dex/pairs/{chainId}/{pairAddress}` (GET) | Get a specific pair by chain and pair contract address | chainId, pairAddress (path) | None | No |

## Parameters

- **q**: Token name, symbol, or contract address
- **tokenAddress**: EVM contract address (or comma-separated for multiple, max 30)
- **chainId**: Blockchain ID (e.g. `ethereum`, `bsc`, `solana`, `arbitrum`)
- **pairAddress**: Pair/pool contract address

## Response Fields (per pair)

| Field | Type | Description |
|-------|------|-------------|
| `chainId` | string | Blockchain |
| `dexId` | string | DEX name (e.g. `uniswap`, `pancakeswap`) |
| `baseToken.symbol` | string | Base token symbol |
| `quoteToken.symbol` | string | Quote token symbol |
| `priceUsd` | string | Current price in USD |
| `priceChange.h24` | float | 24h price change % |
| `liquidity.usd` | float | Total liquidity in USD |
| `volume.h24` | float | 24h trading volume in USD |
| `txns.h24.buys` | integer | 24h buy transactions |
| `txns.h24.sells` | integer | 24h sell transactions |

## Notes

- Coverage: 50+ chains, 100+ DEXes. Near real-time data.
- Use pair search before token launch for early on-chain price discovery.
