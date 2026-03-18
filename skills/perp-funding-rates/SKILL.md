---
name: perp-funding-rates
description: Query perpetual futures funding rates and open interest from the Bybit public API. Track current and historical funding rates to gauge market leverage and directional bias. Free tier, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# Perpetual Funding Rates

Query perpetual futures funding rates and open interest from the Bybit public API.

**Base URL:** `https://api.bybit.com`
**Authentication:** None required.

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/v5/market/tickers` (GET) | Current funding rate, mark price, open interest, and 24h volume for linear/inverse perps | category | symbol | No |
| `/v5/market/funding/history` (GET) | Historical funding rate settlements for a symbol | category, symbol | startTime, endTime, limit | No |

## Parameters

- **category**: `linear` (USDT-margined) or `inverse` (coin-margined)
- **symbol**: Trading pair (e.g. `BTCUSDT`). Omit from `/tickers` to get all symbols.
- **startTime** / **endTime**: Unix timestamp in milliseconds
- **limit**: Max records (default: `200`, max: `200`)

## Response Fields (`/v5/market/tickers`, per ticker)

| Field | Type | Description |
|-------|------|-------------|
| `symbol` | string | Trading pair (e.g. `BTCUSDT`) |
| `fundingRate` | string | Current funding rate (e.g. `0.0001` = 0.01% per 8h) |
| `nextFundingTime` | string | Unix ms timestamp of next funding settlement |
| `markPrice` | string | Mark price used for liquidations |
| `openInterest` | string | Open interest in base currency |
| `openInterestValue` | string | Open interest value in USD |
| `volume24h` | string | 24h trading volume |

## Notes

- Positive rate: longs pay shorts (bullish bias). Negative rate: shorts pay longs (bearish bias).
- Extreme threshold: rates > 0.1% or < -0.1% per 8h signal market imbalance.
- Funding settles every 8 hours. Annualized rate = rate × 3 × 365.
