# Changelog

All notable changes to the KuCoin Assets skill will be documented in this file.

## [1.0.0] - 2026-03-14

### Added

- Initial release of the KuCoin Assets skill.
- Pro API endpoints for unified account overview, balances, transfers, ledgers, interest history, and deposit addresses.
- Classic API endpoints for account info, account detail, account list, and futures account overview.
- Classic API endpoints for account ledgers (Spot, Margin, Trade_hf, Margin_hf, Futures).
- Sub-account management: create sub-accounts, enable margin/futures permissions, list and view sub-account balances.
- Sub-account API management: list, create, delete, and modify sub-account API keys.
- Deposit endpoints: create deposit address (V3), get deposit address (V3), get deposit history.
- Withdrawal endpoints: get withdrawal quotas, withdraw (V3), cancel withdrawal, get withdrawal history.
- Transfer endpoints: get transfer quotas, flex transfer.
- Trade fee endpoints: get basic fee (Spot/Margin), get actual fee (Futures).
- HMAC-SHA256 authentication reference documentation.
- Security rules for credential handling, withdrawal/transfer confirmation, and sub-account operations.
