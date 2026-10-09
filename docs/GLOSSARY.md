# Glossary — StellarRail ubiquitous language

| Term | Meaning |
|---|---|
| **Approver** | RBAC role that reviews `PENDING_APPROVAL` requests and approves/rejects. Cannot approve own requests (SoD). |
| **Operator** | RBAC role that creates payment requests and tracks status. |
| **Admin** | RBAC role that manages users, limits, network config, and views audit/reconciliation. |
| **Maker-checker** | Separation-of-duties pattern: maker (operator) ≠ checker (approver). Enforced in API (`403 SOD_VIOLATION`). |
| **Escrow contract** | Soroban contract holding XLM between request and settlement. Testnet: `CDAV32…2K`. |
| **`deposit`** | Contract entrypoint locking XLM for a `request_id`. Emits `FundsLocked`. |
| **`release`** | Admin/signer entrypoint paying the destination on approval. Emits `PaymentReleased`. |
| **`refund`** | Admin/signer entrypoint returning funds to the depositor on rejection. Emits `PaymentRefunded`. |
| **`expire`** | Permissionless entrypoint returning funds past `deadline`. Emits `RequestExpired`. Works while paused. |
| **Hybrid custody** | API manages signing convenience via KMS/Vault, but funds are isolated on-chain in escrow. |
| **SoD** | Separation of duties — see maker-checker. |
| **Reconciliation** | BullMQ worker matching Postgres ledger rows to Horizon/RPC events; produces `ReconciliationRun` rows. |
| **Audit hash-chain** | Append-only `AuditLog` rows linked by `prevHash/hash/payloadHash` for tamper evidence. |
| **Idempotency key** | Client-supplied `Idempotency-Key` preventing duplicate payments on retry (24 h). |
| **SAC** | Stellar Asset Contract — the contract moves XLM via `token::Client::transfer`. v1 is XLM-only. |
| **Horizon / RPC** | Stellar read APIs: Horizon (accounts/tx history) and Soroban RPC (contract invocation/events). |
| **Dry run** | `CHAIN_DRY_RUN=true` mode where the API builds but does not submit transactions. Default in tests. |
| **PITR / RPO / RTO** | Point-in-time recovery; recovery-point objective (< 1 h); recovery-time objective (< 4 h). |
| **RC / GA** | Release candidate / general availability. Current: `1.0.0-rc1`. |
| **ADR** | Architecture Decision Record (`stellarrail-contracts/docs/adr/`). Required for contract interface changes. |
