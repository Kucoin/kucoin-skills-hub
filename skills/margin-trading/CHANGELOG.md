# Changelog

All notable changes to the KuCoin Margin Trading skill will be documented in this file.

## [1.0.0] - 2026-03-14

### Added

- Initial release of the KuCoin Margin Trading skill.
- Margin market data endpoints (cross margin symbols, isolated margin symbols, ETF info, margin config, mark prices, collateral ratios, available inventory).
- Margin account endpoints (cross margin accounts, isolated margin accounts).
- Margin HF order endpoints (place, cancel, query by orderId/clientOid, open orders, closed orders, trade history).
- Margin stop order endpoints (place, cancel, query stop orders).
- Margin OCO order endpoints (place, cancel, query OCO orders).
- Margin debit endpoints (borrow, repay, interest rate, borrow/repay history, leverage modification).
- Margin credit/lending endpoints (loan market, purchase, modify purchase, redeem, order queries).
- Margin risk limit endpoint.
- HMAC-SHA256 authentication reference.
- Security rules for credential handling.
- Agent behavior rules for margin trading operations.
