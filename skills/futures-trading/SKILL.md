---
name: futures-trading
description: KuCoin Futures trading using the KuCoin API. Futures market data, orders, positions, and funding fees. Authentication requires API Key, API Secret, and Passphrase.
metadata:
  version: 1.0.0
  author: KuCoin
license: MIT
---

# KuCoin Futures Trading Skill

Futures trading on KuCoin using authenticated and public API endpoints. Requires API Key, API Secret, and Passphrase for authenticated endpoints. Return the result in JSON format.

> **Note:** This skill only supports Classic REST API GET endpoints (read-only operations).

## Quick Reference

### Classic Futures -- Market Data

Base URL: `https://api-futures.kucoin.com`

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/api/v1/timestamp` (GET) | Get Server Time | None | None | No |
| `/api/v1/status` (GET) | Get Service Status | None | None | No |
| `/api/v1/mark-price/{symbol}/current` (GET) | Get Mark Price | symbol (path) | None | No |
| `/api/v1/contracts/{symbol}` (GET) | Get Symbol (contract info) | symbol (path) | None | No |
| `/api/v1/contracts/active` (GET) | Get All Symbols (all tradable contracts) | None | None | No |
| `/api/v1/ticker` (GET) | Get Ticker | symbol | None | No |
| `/api/v1/allTickers` (GET) | Get All Tickers | None | None | No |
| `/api/v1/level2/snapshot` (GET) | Get Full OrderBook | symbol | None | No |
| `/api/v1/level2/depth{size}` (GET) | Get Part OrderBook (20 or 100 levels) | size (path: 20 or 100), symbol | None | No |
| `/api/v1/trade/history` (GET) | Get Trade History (last 100 trades) | symbol | None | No |
| `/api/v1/kline/query` (GET) | Get Klines (candlestick data) | symbol, granularity | from, to | No |
| `/api/v1/index/query` (GET) | Get Spot Index Price | symbol | startAt, endAt, reverse, offset, forward, maxCount | No |
| `/api/v1/interest/query` (GET) | Get Interest Rate Index | symbol | startAt, endAt, reverse, offset, forward, maxCount | No |
| `/api/v1/premium/query` (GET) | Get Premium Index | symbol | startAt, endAt, reverse, offset, forward, maxCount | No |
| `/api/v1/trade-statistics` (GET) | Get 24hr Stats (platform futures volume). **Must use Classic Futures base URL (`https://api-futures.kucoin.com`).** | None | None | Yes |

### Classic Futures -- Order Queries

Base URL: `https://api-futures.kucoin.com`

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/api/v1/stopOrders` (GET) | Get Stop Order List | None | symbol, side, type, startAt, endAt, currentPage, pageSize | Yes |
| `/api/v1/orders/{order-id}` (GET) | Get Order By OrderId | order-id (path) | None | Yes |
| `/api/v1/orders/byClientOid` (GET) | Get Order By ClientOid | clientOid | None | Yes |
| `/api/v1/recentDoneOrders` (GET) | Get Recent Closed Orders (last 24h) | None | symbol | Yes |
| `/api/v1/openOrderStatistics` (GET) | Get Open Order Value | symbol | None | Yes |
| `/api/v1/recentFills` (GET) | Get Recent Trade History (last 24h fills) | None | symbol | Yes |

### Classic Futures -- Position Queries

Base URL: `https://api-futures.kucoin.com`

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/api/v2/position/getMarginMode` (GET) | Get Margin Mode | symbol | None | Yes |
| `/api/v2/position/getPositionMode` (GET) | Get Position Mode | None | None | Yes |
| `/api/v2/getMaxOpenSize` (GET) | Get Max Open Size | symbol, price, leverage | None | Yes |
| `/api/v2/position` (GET) | Get Position Details | symbol | None | Yes |
| `/api/v1/positions` (GET) | Get Position List | None | currency | Yes |
| `/api/v1/history-positions` (GET) | Get Positions History | None | symbol, from, to, limit, pageId | Yes |
| `/api/v1/margin/maxWithdrawMargin` (GET) | Get Max Withdraw Margin | symbol | positionSide | Yes |
| `/api/v2/getCrossUserLeverage` (GET) | Get Cross Margin Leverage | symbol | None | Yes |
| `/api/v2/batchGetCrossOrderLimit` (GET) | Get Cross Margin Risk Limit | symbol | totalMargin, leverage | Yes |
| `/api/v1/contracts/risk-limit/{symbol}` (GET) | Get Isolated Margin Risk Limit | symbol (path) | None | No |

### Classic Futures -- Funding Fees

Base URL: `https://api-futures.kucoin.com`

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/api/v1/funding-rate/{symbol}/current` (GET) | Get Current Funding Rate | symbol (path) | None | No |
| `/api/v1/contract/funding-rates` (GET) | Get Public Funding History | symbol, from, to | None | No |
| `/api/v1/funding-history` (GET) | Get Private Funding History | symbol | startAt, endAt, reverse, offset, forward, maxCount | Yes |

---

## Parameters

### Common Parameters

* **symbol**: Symbol of the contract (e.g., `XBTUSDTM`, `ETHUSDTM`). Path or query parameter depending on endpoint.
* **clientOid**: User-defined unique order ID, max 40 characters.
* **startAt / from**: Begin time in milliseconds.
* **endAt / to**: End time in milliseconds.
* **pageSize**: Number of records per page. Default varies by endpoint (typically 50, max 200).
* **currentPage**: Current page number for paginated queries. Default: 1.
* **side**: Order side filter: `buy` | `sell`
* **type**: Order type filter: `limit` | `market`
* **currency**: Currency code filter (e.g., `USDT`, `XBT`).
* **reverse**: Boolean. Reverse the order of results.
* **offset**: Start offset for pagination.
* **forward**: Boolean. `true` for forward lookup (default), `false` for backward.
* **maxCount**: Displayed size per page. Default 50.
* **granularity**: Candlestick interval in minutes: `1`, `5`, `15`, `30`, `60`, `120`, `240`, `480`, `720`, `1440`, `10080`
* **positionSide**: Position side filter: `BOTH` | `LONG` | `SHORT`
* **price**: Price parameter for max open size query.
* **leverage**: Leverage parameter for max open size and risk limit queries.
* **totalMargin**: Total margin for cross margin risk limit query.

### Enums

* **side**: `buy` | `sell`
* **type**: `limit` | `market`
* **positionSide**: `BOTH` (One-Way mode) | `LONG` (Hedge mode long) | `SHORT` (Hedge mode short)
* **marginMode**: `ISOLATED` | `CROSS`
* **positionMode**: `0` (One-Way mode) | `1` (Hedge mode)
* **status** (order): `open` | `done`
* **serverStatus**: `open` | `close` | `cancelonly`
* **granularity** (minutes): `1` | `5` | `15` | `30` | `60` | `120` | `240` | `480` | `720` | `1440` | `10080`

---

## Authentication

For endpoints that require authentication, you will need to provide KuCoin API credentials.

Required credentials:
* **API Key**: Your KuCoin API key
* **API Secret**: Your KuCoin API secret (for signing)
* **API Passphrase**: Your KuCoin API passphrase (set when creating the API key)

**IMPORTANT -- Base URL:**
* **Classic Futures API**: `https://api-futures.kucoin.com` -- Used for all Classic Futures REST endpoints (`/api/v1/...`, `/api/v2/...`, `/api/v3/...`)

---

## Security

### Share Credentials

Users can provide KuCoin API credentials by sending a file where the content is in the following format:

```
api_key_value
api_secret_value
api_passphrase_value
```

### Never Disclose API Key and Secret

Never disclose the location of the API key and secret file.

Never send the API key and secret to any website other than the official KuCoin API base URLs.

### Never Display Full Secrets

When showing credentials to users:
- **API Key:** Show first 5 + last 4 characters: `abcde...wxyz`
- **API Secret:** Always mask, show only last 5: `***...s1k2y`
- **Passphrase:** Always fully masked: `***...`

Example response when asked for credentials:
Account: main
API Key: abcde...wxyz
Secret: ***...s1k2y
Passphrase: ***...
Environment: Production

### Listing Accounts

When listing accounts, show names and environment only -- never keys:
KuCoin Accounts:
* main (Production)
* test-account (Production)

### Transactions Confirmation

When performing trading operations (placing orders, canceling orders, modifying positions), always confirm with the user before proceeding by asking them to write "CONFIRM" to proceed.

---

## KuCoin Accounts

### main
- API Key: your_api_key
- Secret: your_api_secret
- Passphrase: your_passphrase
