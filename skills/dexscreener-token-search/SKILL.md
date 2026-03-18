---
name: dexscreener-token-search
description: Search for DEX trading pairs and get real-time on-chain price data for any token across all major DEXes using the DexScreener API. Shows price, liquidity, volume, and price change across 50+ chains. Free, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# DexScreener Token Search

Search for any token across all DEXes and chains to get real-time on-chain price data, liquidity, and trading activity.

**Base URL:** `https://api.dexscreener.com`
**Authentication:** None required (free public API).

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/latest/dex/search` (GET) | Search pairs by token name, symbol, or contract address | q | None | No |
| `/latest/dex/tokens/{tokenAddresses}` (GET) | Get all pairs for one or more contract addresses (comma-separated, max 30) | tokenAddresses (path) | None | No |

## Parameters

- **q**: Token name, symbol, or contract address (e.g. `PEPE`, `0xabc...`)
- **tokenAddresses**: Comma-separated EVM contract addresses (path parameter)

## Response Fields (per pair)

| Field | Type | Description |
|-------|------|-------------|
| `chainId` | string | Blockchain (e.g. `ethereum`, `bsc`, `solana`) |
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

- Near real-time data with seconds latency.
- Coverage: 50+ chains including Ethereum, BSC, Solana, Arbitrum, Base, Polygon.
- No official rate limit stated; generous for free use.
