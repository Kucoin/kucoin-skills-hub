# KuCoin Skills Hub

An open skills marketplace that gives AI agents read-only access to the KuCoin exchange via the KuCoin Classic REST API.

> **Note:** All skills currently support **GET (read-only) endpoints only**. Write operations (placing/cancelling orders, transfers, etc.) are not yet supported.

## Quick Start

Install all skills at once:

```bash
npx skills add https://github.com/Kucoin/kucoin-skills-hub --full-depth
```

Or install a specific skill:

```bash
npx skills add https://github.com/Kucoin/kucoin-skills-hub --full-depth --skill spot
```

> **Why `--full-depth`?** The `skills` CLI stops scanning at the first directory level by default. Since each skill lives under `skills/<name>/SKILL.md`, `--full-depth` is required to discover them.

## Available Skills

| Skill | Version | Description |
|-------|---------|-------------|
| [spot](./skills/spot) | 1.0.0 | Spot market data, HF order queries, stop orders, and OCO orders |
| [margin-trading](./skills/margin-trading) | 1.0.0 | Cross/isolated margin market data, HF order queries, stop orders, OCO orders, borrow/repay history, lending, and risk limits |
| [futures-trading](./skills/futures-trading) | 1.0.0 | Futures market data, order queries, position queries, and funding fees |
| [assets](./skills/assets) | 1.0.0 | Account balances, ledgers, sub-accounts, deposits, withdrawals, transfers, and trade fees |
| [earn](./skills/earn) | 1.0.0 | Simple Earn (savings, staking, promotions) and Structured Earn (dual investment) product queries |
| [convert](./skills/convert) | 1.0.0 | Convert currencies, quotes, market order history, and limit order queries |
| [broker](./skills/broker) | 1.0.0 | Affiliate invitee/commission queries, Broker Pro rebate/user queries, and ND Exchange Broker sub-account queries |

## Skill Coverage

<details>
<summary><strong>spot</strong> — Spot Trading</summary>

- **Market Data**: announcements, currencies, symbols, tickers, order book (L1/L2/full), trade history, klines, call auction, fiat prices, 24hr stats, market list
- **HF Order Queries**: get order by ID/clientOid, open orders, closed orders, trade fills, DCP settings
- **Stop Order Queries**: list, get by orderId/clientOid
- **OCO Order Queries**: get by orderId/clientOid, order list, order detail

</details>

<details>
<summary><strong>margin-trading</strong> — Margin Trading</summary>

- **Market Data**: cross/isolated margin symbols, ETF info, margin config, mark price list, collateral ratio, available inventory
- **HF Order Queries**: get by orderId/clientOid, open orders (by symbol), closed orders, trade fills
- **Stop Order Queries**: list, get by orderId/clientOid
- **OCO Order Queries**: get by orderId/clientOid, order list, order detail
- **Debit (Borrow/Repay)**: borrow interest rate, borrow history, repay history, interest history
- **Credit (Lending)**: loan market, market interest rate, purchase orders, redeem orders
- **Risk Limit**: margin risk limit per currency/symbol

</details>

<details>
<summary><strong>futures-trading</strong> — Futures Trading</summary>

- **Market Data**: server time, service status, mark price, contracts (single/all), tickers, order book (full/partial), trade history, klines, spot index, interest rate index, premium index, 24hr stats
- **Order Queries**: stop orders, get order by orderId/clientOid, recent closed orders, open order value, recent fills
- **Position Queries**: margin mode, position mode, max open size, position details, position list, position history, max withdraw margin, cross leverage, cross risk limit, isolated risk limit
- **Funding Fees**: current funding rate, public funding history, private funding history

</details>

<details>
<summary><strong>assets</strong> — Account & Assets</summary>

- **Account & Funding**: account summary, API key info, account type, account list/detail (spot/cross margin/isolated margin/futures), account ledgers (spot, trade_hf, margin_hf, futures)
- **Sub-Accounts**: sub-account list (summary/spot balance/futures balance), sub-account detail, sub-account API keys
- **Deposit**: deposit address (V3), deposit history
- **Withdrawals**: withdrawal quotas, withdrawal history (by ID / list)
- **Transfer**: transfer quotas
- **Trade Fee**: basic fee (spot/margin), actual fee (futures)

</details>

<details>
<summary><strong>earn</strong> — Earn Products</summary>

- **Simple Earn**: redeem preview, savings products, promotion products, staking products, KCS staking products, ETH staking products, account holdings
- **Structured Earn**: structured product orders, dual investment products *(endpoints may be unavailable — returns 400100)*

</details>

<details>
<summary><strong>convert</strong> — Instant Convert</summary>

- Convert symbol info, available currencies, market quote, market order detail/history
- Limit convert quote, limit order detail, limit order list

</details>

<details>
<summary><strong>broker</strong> — Broker & Affiliate</summary>

- **Affiliate**: invitee list, commission records, trade history by UID/time, KuMining commission
- **Broker Pro**: rebate download, commission records, user list, user transactions
- **ND Exchange Broker**: KYC status, broker info, sub-account list/API keys, transfer history, deposit list/detail, withdrawal detail, rebate download *(some endpoints may return 404 — see skill notes)*

</details>

## Base URLs

| Service | URL |
|---------|-----|
| REST API | `https://api.kucoin.com` |
| Futures REST API | `https://api-futures.kucoin.com` |

> Futures endpoints must use `https://api-futures.kucoin.com`. This is noted individually in the relevant skill.

## Authentication

Authenticated endpoints require the following HTTP headers:

| Header | Value |
|--------|-------|
| `KC-API-KEY` | Your API Key |
| `KC-API-SIGN` | HMAC-SHA256 signature of the request, Base64 encoded |
| `KC-API-TIMESTAMP` | Current timestamp in milliseconds |
| `KC-API-PASSPHRASE` | Passphrase encrypted with your Secret Key (HMAC-SHA256, Base64 encoded) |
| `KC-API-KEY-VERSION` | API Key version — use `3` |

For full signing logic and code examples, see each skill's [`references/authentication.md`](./skills/spot/references/authentication.md).

## Response Format

All KuCoin API responses return a consistent JSON envelope:

```json
{
  "code": "200000",
  "data": { ... }
}
```

`"200000"` indicates success. Any other code indicates an error.

## Skill Format

Each skill follows this structure:

```
skills/<skill-name>/
├── SKILL.md              # Skill definition (YAML frontmatter + endpoint reference)
├── CHANGELOG.md          # Version history
├── LICENSE.md            # License
└── references/
    └── authentication.md # Authentication & signing guide
```

## Contributing

1. Fork this repository
2. Create a new skill folder under `skills/` following the structure above
3. Add a `SKILL.md` with YAML frontmatter (`name`, `description`, `metadata`, `license`) and a Markdown endpoint reference table
4. Open a Pull Request

## Disclaimer

This project is an independent, community-maintained integration and is **not officially affiliated with, endorsed by, or sponsored by KuCoin**. KuCoin is a registered trademark of its respective owner.

- **No financial advice.** Nothing in this repository constitutes investment, financial, trading, or any other form of advice. All API data retrieved through these skills is for informational purposes only.
- **No warranty.** This software is provided "as is", without warranty of any kind, express or implied. The authors and contributors make no representations regarding accuracy, completeness, or fitness for a particular purpose.
- **API changes.** KuCoin may modify, deprecate, or remove API endpoints at any time without notice. Some endpoints documented here may already be unavailable or return errors.
- **Use at your own risk.** You are solely responsible for any actions taken based on data retrieved through these skills, including but not limited to trading decisions, financial losses, or security incidents arising from misuse of API credentials.
- **Security.** Never expose your API Key, Secret, or Passphrase in public repositories, logs, or client-side code. Always follow the security guidelines in each skill's `references/authentication.md`.
- **Rate limits.** KuCoin enforces API rate limits. Excessive requests may result in temporary or permanent IP/key bans. You are responsible for staying within the allowed limits.

By using this project, you agree to these terms and accept all associated risks.

## License

MIT
