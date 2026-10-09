# StellarRail Roadmap

Current: **`1.0.0-rc1`** (Testnet pilot, Oct 2026). Pre-audit self-assessment
complete; external audit pending — **no mainnet** until it lands.

## v1.0 — in scope (this RC)

- XLM-only payments on Testnet (+ gated Mainnet pilot infra).
- 3-role RBAC: Admin / Operator / Approver, JWT + TOTP MFA.
- Soroban escrow v1: `deposit` / `release` / `refund` / `expire`,
  admin rotation, pause, upgrades.
- Web dashboard: payments, approvals (+history), admin
  (users/limits/network/health/audit), account, notifications.
- Reconciliation workers, hash-chained audit logs (CSV/JSON export),
  Prometheus/Sentry observability.
- KPIs: settlement < 10 min, error rate < 0.1%, 100% audit coverage.

### Non-goals (v1.0)

Custom assets (USDC…), multi-sig outside contract logic, fiat ramps,
batch payments, consumer wallets, cross-chain.

## v1.1 — next

- USDC (Stellar Asset Contract) support alongside XLM.
- Batch payment processor (one approval → many releases).
- Contract: additive entrypoints only (frozen v1 stays compatible) + ADR.

## v2.0 — later

- Multi-tenant SaaS: org isolation, per-org limits/keys/policies.
- Mobile approver companion (push approvals + biometrics).
- Advanced policy engine (thresholds, time locks, allowlists).

## Release train

`Alpha (Testnet, unstable) → Beta (Testnet, hardened) → RC1 (Mainnet pilot,
read-only then small-value) → GA (full production + marketing site).`

We are at **RC1**. GA requires: external audit clean, mainnet ceremony
complete (`DEPLOY_MAINNET.md`), 99.9% API uptime in pilot, and
reconciliation with zero unexplained mismatches for 30 days.
