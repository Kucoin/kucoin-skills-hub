---
name: nft-floor-price
description: Query NFT collection floor prices, listing counts, and 7-day price trends using the DefiLlama NFT API. Supports listing all collections and fetching metadata for a specific collection by contract address. Free, no authentication required.
metadata:
  version: 1.0.0
  author: Bob-QoQ
license: MIT
---

# NFT Collection Floor Price

Query NFT collection floor prices, listing counts, and 7-day price trends using the DefiLlama NFT API.

**Base URL:** `https://nft.llama.fi`
**Authentication:** None required (free public API).

## Quick Reference

| Endpoint | Description | Required | Optional | Authentication |
|----------|-------------|----------|----------|----------------|
| `/collections` (GET) | All tracked NFT collections with current floor price, supply, and trend data | None | None | No |
| `/collection/{collectionId}` (GET) | Metadata and floor price details for a single collection by contract address | collectionId (path) | None | No |

## Parameters

- **collectionId**: EVM contract address of the collection (from `collectionId` field in `/collections`)

## Response Fields (per collection, `/collections`)

| Field | Type | Description |
|-------|------|-------------|
| `collectionId` | string | Collection contract address (EVM) |
| `name` | string | Collection display name |
| `symbol` | string | Collection symbol |
| `totalSupply` | number | Total NFTs minted |
| `onSaleCount` | number | NFTs currently listed for sale |
| `floorPrice` | number | Current floor price in ETH |
| `floorPricePctChange1Day` | number | Floor price % change (24h) |
| `floorPricePctChange7Day` | number | Floor price % change (7d) |

## Notes

- Floor prices are denominated in ETH.
- Use `collectionId` (contract address) to query individual collection details.
