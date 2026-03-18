---
name: token-safety-check
description: Analyze ERC-20 token contracts and wallet addresses for security risks using the GoPlus Security API. Detects honeypots, malicious ownership patterns, high taxes, and known wallet threats across EVM chains. Free, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# Token Safety Check

Analyze ERC-20 token contracts and wallet addresses for security risks using the GoPlus Security API.

**Base URL:** `https://api.gopluslabs.io/api/v1`
**Authentication:** None required (free public API).

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/token_security/{chain_id}` (GET) | Security analysis for one or more token contracts | chain_id (path), contract_addresses | None | No |
| `/address_security/{address}` (GET) | Risk analysis for a wallet address (scam, phishing, mixer) | address (path) | None | No |

## Parameters

- **chain_id**: EVM chain ID — `1` (Ethereum), `56` (BSC), `137` (Polygon), `42161` (Arbitrum), `8453` (Base), `10` (Optimism)
- **contract_addresses**: Token contract address, or comma-separated list (max 50)
- **address**: Wallet address to analyze

## Key Token Security Fields

| Field | Values | Description |
|-------|--------|-------------|
| `is_honeypot` | `0`, `1` | Tokens cannot be sold (critical red flag) |
| `is_open_source` | `0`, `1` | Contract code is verified on-chain |
| `is_mintable` | `0`, `1` | New tokens can be minted (inflation risk) |
| `hidden_owner` | `0`, `1` | Hidden owner exists (dangerous) |
| `buy_tax` | decimal | Buy fee (e.g. `0.05` = 5%) |
| `sell_tax` | decimal | Sell fee (e.g. `0.10` = 10%) |
| `owner_percent` | decimal | Owner's share of total supply |
| `is_blacklisted` | `0`, `1` | Contract has blacklist functionality |
| `is_whitelisted` | `0`, `1` | Contract has whitelist functionality |
| `lp_holders` | array | Top LP holders and their lock status |

## Chain ID Reference

| Chain | ID |
|-------|----|
| Ethereum | `1` |
| BSC | `56` |
| Polygon | `137` |
| Arbitrum | `42161` |
| Base | `8453` |
| Optimism | `10` |

## Notes

- Can scan up to 50 token addresses in a single request.
- All flag fields use string `"0"` (false) and `"1"` (true) — not booleans.
- `sell_tax` > 10% or `is_honeypot` = `"1"` are critical risk signals.
