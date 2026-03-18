---
name: cross-chain-bridge-stats
description: Query cross-chain bridge volume, rankings, and transaction data using the DefiLlama Bridges API. Returns 24h/7d/monthly volumes and supported chains for all tracked bridges. Free tier only.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# Cross-Chain Bridge Stats

Query cross-chain bridge volume, rankings, and transaction data using the DefiLlama Bridges API.

**Base URL:** `https://bridges.llama.fi`
**Authentication:** None required for `/bridges` (free tier).

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/bridges` (GET) | All tracked bridges with 24h/7d/30d volume and chain support | None | includeChains | No |
| `/bridge/{id}` (GET) | Detailed info for a single bridge by ID | id (path) | None | Paid plan |
| `/bridgevolume/{chain}` (GET) | Historical bridge volume for a specific chain | chain (path) | None | Paid plan |
| `/transactions/{id}` (GET) | Recent bridge transactions for a given bridge ID | id (path) | None | Paid plan |

## Parameters

- **includeChains**: `true` to include the `chains` array per bridge (supported chains list)
- **id**: Bridge ID integer from the `id` field in `/bridges`
- **chain**: Chain name (e.g. `Ethereum`, `Arbitrum`, `Polygon`)

## Response Fields (per bridge, `/bridges`)

| Field | Type | Description |
|-------|------|-------------|
| `id` | integer | Unique bridge ID |
| `displayName` | string | Human-readable name (e.g. `Circle CCTP`) |
| `last24hVolume` | float | Volume (USD) in last 24 hours |
| `lastDailyVolume` | float | Volume (USD) for last full calendar day |
| `weeklyVolume` | float | Volume (USD) over last 7 days |
| `monthlyVolume` | float | Volume (USD) over last 30 days |
| `chains` | string[] | Supported chains (requires `includeChains=true`) |

## Notes

- Only `/bridges` is available on the free tier.
- Bridge volume data updates approximately every hour.
