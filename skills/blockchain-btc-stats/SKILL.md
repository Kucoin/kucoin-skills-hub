---
name: blockchain-btc-stats
description: Fetch live Bitcoin network statistics including hashrate, mining difficulty, mempool size, block time, and market price using the Blockchain.com public API. Free, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# Bitcoin Network Stats

Monitor Bitcoin's network health: hashrate, difficulty, mempool, block time, and more.

**Base URL:** `https://blockchain.info`
**Authentication:** None required (free public API).

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/stats` (GET) | Full Bitcoin network snapshot: hashrate, difficulty, block time, price, mempool | format=json | None | No |
| `/mempool/count` (GET) | Current unconfirmed transaction count in the mempool | format=json | None | No |

## Parameters

- **format**: Always pass `json` to get JSON response instead of plain text.

## Key Response Fields (`/stats`)

| Field | Type | Description |
|-------|------|-------------|
| `market_price_usd` | float | Current BTC price in USD |
| `hash_rate` | float | Network hashrate (GH/s) |
| `difficulty` | float | Current mining difficulty |
| `minutes_between_blocks` | float | Average block interval (minutes) |
| `n_blocks_total` | integer | Total block height |
| `n_blocks_mined` | integer | Blocks mined in last 24h |
| `mempool_size` | integer | Unconfirmed transactions in mempool |
| `estimated_transaction_volume_usd` | float | 24h on-chain volume (USD) |

## Notes

- Stats update approximately every 10 minutes.
- Satoshi values — divide by 100,000,000 to convert to BTC.
