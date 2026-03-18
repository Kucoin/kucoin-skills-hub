---
name: defi-tvl-overview
description: Query Total Value Locked (TVL) data across DeFi protocols and blockchains using the DefiLlama API. Supports protocol listings, TVL history, global DeFi charts, and per-chain TVL breakdowns. Free, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# DeFi TVL Overview

Query Total Value Locked (TVL) data across DeFi protocols and blockchains using the DefiLlama API.

**Base URL:** `https://api.llama.fi`
**Authentication:** None required (free public API).

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/protocols` (GET) | All tracked DeFi protocols with current TVL and metadata | None | None | No |
| `/protocol/{name}` (GET) | TVL history and chain breakdown for a single protocol | name (path) | None | No |
| `/charts` (GET) | Global DeFi total TVL chart (daily, all history) | None | None | No |
| `/chains` (GET) | TVL by blockchain (current + breakdown) | None | None | No |
| `/v2/historicalChainTvl/{chain}` (GET) | Historical TVL for a specific chain | chain (path) | None | No |

## Parameters

- **name**: Protocol slug (e.g. `aave`, `uniswap`, `lido`) — use `slug` field from `/protocols`
- **chain**: Chain name (e.g. `Ethereum`, `BSC`, `Solana`) — case-sensitive

## Response Fields (per protocol, `/protocols`)

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Protocol display name |
| `slug` | string | URL-safe identifier (use in `/protocol/{name}`) |
| `symbol` | string | Native token symbol |
| `chain` | string | Primary chain |
| `chains` | array | All deployed chains |
| `category` | string | Protocol category (e.g. `Lending`, `DEX`, `Yield`) |
| `tvl` | number | Current TVL in USD |
| `chainTvls` | object | TVL breakdown by chain |

## Notes

- All data is free and unauthenticated.
- Use `slug` (not `name`) when querying `/protocol/{name}`.
