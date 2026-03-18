---
name: fear-greed-index
description: Retrieve the Crypto Fear & Greed Index and historical values using the Alternative.me API. Measures market sentiment from 0 (Extreme Fear) to 100 (Extreme Greed). Free tier, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# Crypto Fear & Greed Index

Retrieve the Crypto Fear & Greed Index — a composite market sentiment indicator ranging from 0 (Extreme Fear) to 100 (Extreme Greed).

**Base URL:** `https://api.alternative.me`
**Authentication:** None required.

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/fng/` (GET) | Current Fear & Greed Index value and classification | None | limit, format, date_format | No |
| `/fng/?limit={n}` (GET) | Historical Fear & Greed Index (last n days, most recent first) | limit | format, date_format | No |

## Parameters

- **limit**: Number of data points to return. Default: `1` (current only). `0` = all history.
- **format**: `json` (default) or `csv`
- **date_format**: `us`, `cn`, `kr`, or `world` (ISO 8601). Default: Unix timestamp.

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `value` | string | Index value (0–100) |
| `value_classification` | string | `Extreme Fear`, `Fear`, `Neutral`, `Greed`, or `Extreme Greed` |
| `timestamp` | string | Unix timestamp of the reading |
| `time_until_update` | string | Seconds until next update (current only) |

## Classification Ranges

| Range | Label | Signal |
|-------|-------|--------|
| 0–24 | Extreme Fear | Historically a buy signal |
| 25–44 | Fear | Market is fearful |
| 45–55 | Neutral | Balanced sentiment |
| 56–75 | Greed | Market is greedy |
| 76–100 | Extreme Greed | Historically a sell signal |

## Notes

- Updates once daily at midnight UTC.
- Full history available back to 2018.
- Index is BTC-centric; primarily reflects Bitcoin market sentiment.
