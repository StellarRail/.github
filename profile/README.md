<div align="center">

# StellarRail

**Secure payment operations on Stellar — request · lock · approve · settle.**

[![API CI](https://github.com/StellarRail/stellarrail-api/actions/workflows/ci.yml/badge.svg)](https://github.com/StellarRail/stellarrail-api/actions/workflows/ci.yml)
[![Contracts CI](https://github.com/StellarRail/stellarrail-contracts/actions/workflows/ci.yml/badge.svg)](https://github.com/StellarRail/stellarrail-contracts/actions/workflows/ci.yml)
[![Web CI](https://github.com/StellarRail/stellarrail-web/actions/workflows/ci.yml/badge.svg)](https://github.com/StellarRail/stellarrail-web/actions/workflows/ci.yml)
[![Contracts Security](https://github.com/StellarRail/stellarrail-contracts/actions/workflows/security.yml/badge.svg)](https://github.com/StellarRail/stellarrail-contracts/actions/workflows/security.yml)

*XLM-only v1 · Maker-checker workflow · Soroban escrow guarantees · Full audit trail*

</div>

---

## What is StellarRail?

Teams moving value on Stellar face key-management risk, missing
separation-of-duties, and painful reconciliation. StellarRail is the
**control tower for Stellar payments**:

- **Hybrid custody** — the API manages hot-wallet signing (KMS/Vault),
  but funds are logically isolated in a **Soroban escrow contract**.
- **Maker-checker** — Operators request, Approvers authorize. Nobody can
  move funds alone; an operator can never approve their own request.
- **On-chain guarantees** — funds are `deposit`ed on request, only
  `release`d on approval, `refund`ed on rejection, and `expire`d
  permissionlessly past deadline.
- **Automated reconciliation** — BullMQ workers poll Horizon / Soroban RPC,
  match on-chain events to the Postgres ledger, and flag mismatches.

Target users: internal finance/ops teams, DAO treasuries, small fintechs
running a Testnet pilot today (`1.0.0-rc1`).

## Repositories

| Repo | What lives there | Start here |
|---|---|---|
| [**stellarrail-api**](https://github.com/StellarRail/stellarrail-api) | NestJS 10 API: JWT + MFA auth, 3-role RBAC, payment state machine, chain service, BullMQ reconciliation, audit hash-chain | `cp .env.example .env && docker compose up -d && npm install && npx prisma migrate dev && npm run seed && npm run start:dev` → http://localhost:3000/docs |
| [**stellarrail-contracts**](https://github.com/StellarRail/stellarrail-contracts) | Soroban escrow: `deposit` / `release` / `refund` / `expire`, admin rotation, events, upgrades. Interface **frozen at v1** | `make build && make test && make lint` · Testnet: `CDAV32AHVV6Q7FFNPUFA76AARTGWBE2QFIU3WAPDV64QIBRTGUFKLU2K` |
| [**stellarrail-web**](https://github.com/StellarRail/stellarrail-web) | Next.js 15 dashboard: payments, approvals, admin (users/limits/network/health/audit), mock mode | `cp .env.example .env.local && npm ci && npm run dev` with `NEXT_PUBLIC_USE_MOCK=true` |

Shared org docs live in this repo under [`docs/`](https://github.com/StellarRail/.github/tree/main/docs).

## How it works

```mermaid
graph LR
    OP[Operator<br/>requests payment] --> API[NestJS API<br/>validates + deposits]
    API --> ESC[Soroban Escrow<br/>LOCKED_IN_ESCROW]
    ESC --> AP[Approver<br/>approve / reject]
    AP -->|approve| REL[release() → SETTLED]
    AP -->|reject| REF[refund() → REFUNDED]
    REL --> REC[BullMQ worker<br/>Horizon/RPC reconcile]
    REF --> REC
    EXP[expire() — anyone<br/>past deadline] --> REC
```

Payment lifecycle in the API (Postgres):
`DRAFT → CREATED → LOCKED_IN_ESCROW → PENDING_APPROVAL → SETTLING → SETTLED`
(alternates: `REFUNDING → REFUNDED`, `FAILED`, `EXPIRED`, `TIMEOUT`).

Contract lifecycle on-chain: `Locked → Released | Refunded | Expired`.

## Quickstart (5 minutes, Testnet + mocks)

```bash
# 1 — contracts (read-only, no keys needed)
git clone https://github.com/StellarRail/stellarrail-contracts.git
cd stellarrail-contracts && make build && make test

# 2 — api (needs Docker for Postgres 15 + Redis 7)
git clone https://github.com/StellarRail/stellarrail-api.git
cd stellarrail-api
cp .env.example .env && docker compose up -d
npm install && npx prisma migrate dev && npm run seed && npm run start:dev
# Swagger → http://localhost:3000/docs
# Demo logins (dev only): admin/operator/approver@stellarrail.local / *Pass12345!

# 3 — web (mock mode, no backend needed)
git clone https://github.com/StellarRail/stellarrail-web.git
cd stellarrail-web
cp .env.example .env.local && npm ci --no-audit --no-fund
NEXT_PUBLIC_USE_MOCK=true npm run dev
# Demo logins: operator/approver/admin@stellarrail.test / password123 / MFA 123456
```

> Mainnet is **pilot-gated** (`CONFIRM_MAINNET=I_UNDERSTAND_REAL_FUNDS`,
> external audit pending). Do not run mainnet without completing the
> checklists in `stellarrail-contracts/docs/DEPLOY_MAINNET.md` and
> `stellarrail-api/docs/PRODUCTION.md`.

## Status & roadmap

- **Now:** `1.0.0-rc1` — XLM-only, 3 roles (Admin/Operator/Approver),
  Testnet + staging deploys, pre-audit self-assessment complete.
- **Next (v1.1):** USDC via SAC, batch payments.
- **Later (v2.x):** multi-tenant org isolation, mobile approver companion.

See [`docs/ROADMAP.md`](https://github.com/StellarRail/.github/blob/main/docs/ROADMAP.md)
for scope, non-goals, and KPIs (settlement < 10 min, error rate < 0.1%).

## Security & compliance

- Keys never leave KMS/Vault; no raw keys in env; rate-limited APIs;
  append-only audit logs exportable as CSV/JSON (GDPR `DELETE /users/me`).
- Report vulnerabilities **privately** — see
  [`SECURITY.md`](https://github.com/StellarRail/.github/blob/main/SECURITY.md).
  Please do not open public issues for security reports.

## Contributing & support

- Start with [`CONTRIBUTING.md`](https://github.com/StellarRail/.github/blob/main/CONTRIBUTING.md)
  (Conventional Commits, DCO-free, tests required).
- Code of conduct:
  [`CODE_OF_CONDUCT.md`](https://github.com/StellarRail/.github/blob/main/CODE_OF_CONDUCT.md).
- Need help? See
  [`SUPPORT.md`](https://github.com/StellarRail/.github/blob/main/SUPPORT.md)
  for the support matrix and response targets.

---

<sub>© 2026 StellarRail · API: Apache-2.0 · Contracts: MIT / Apache-2.0 · Web: MIT</sub>
