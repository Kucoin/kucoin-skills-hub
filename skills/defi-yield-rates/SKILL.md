---
name: defi-yield-rates
description: Query real-time yield and APY data across 19,000+ DeFi pools on 80+ chains using DefiLlama's Yields API. Filter by chain, protocol, stablecoin, or IL risk. Includes historical APY chart data per pool. Free, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# DeFi Protocol Yield Rates

Query real-time yield and APY data across 19,000+ DeFi pools on 80+ chains using DefiLlama's Yields API.

**Base URL:** `https://yields.llama.fi`
**Authentication:** None required (free public API).

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/pools` (GET) | All yield pools with current APY, TVL, chain, project, and metadata | None | None | No |
| `/chart/{pool}` (GET) | Historical APY and TVL chart for a specific pool | pool (path, UUID) | None | No |

## Parameters

- **pool**: Pool UUID from the `pool` field in `/pools` response (e.g. `747c1d2a-c668-4682-b9f9-296708a3dd90`)

## Response Fields (per pool, `/pools`)

| Field | Type | Description |
|-------|------|-------------|
| `pool` | string | Pool UUID (use in `/chart/{pool}`) |
| `chain` | string | Blockchain (e.g. `Ethereum`, `Solana`) |
| `project` | string | Protocol slug (e.g. `lido`, `aave-v3`) |
| `symbol` | string | Pool token symbol (e.g. `STETH`, `USDC-USDT`) |
| `tvlUsd` | number | Total value locked in USD |
| `apy` | float | Current total APY % |
| `apyBase` | float | Base APY from trading fees % |
| `apyReward` | float | Reward token APY % |
| `stablecoin` | boolean | Whether the pool is stablecoin-only |
| `ilRisk` | string | Impermanent loss risk: `no`, `low`, `medium`, `high` |
| `exposure` | string | `single` (one token) or `multi` (LP pair) |
| `apyPct1D` | float | APY change over last 1 day |
| `apyPct7D` | float | APY change over last 7 days |
| `apyPct30D` | float | APY change over last 30 days |

## Notes

- Filtering is done client-side on the full dataset returned by `/pools`.
- To find stablecoin yields with no IL risk: filter `stablecoin=true` and `ilRisk=no`.
- Historical chart endpoint provides daily APY and TVL back to pool inception.
