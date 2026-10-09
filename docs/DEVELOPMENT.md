# StellarRail Development Guide (all repos)

## Prerequisites

- Node **20** (`nvm use` in api/web; `.nvmrc` pins `20`).
- Docker + Compose (Postgres 15, Redis 7 for the API).
- Rust **1.97.1** + `wasm32v1-none` target + `stellar-cli` **≥ 27.1.0**
  (contracts).
- No secrets needed for the default read-only / mock loops below.

## Fastest loop per repo

### API — `stellarrail-api`

```bash
cp .env.example .env
docker compose up -d            # postgres:5432 + redis:6379
npm install
npx prisma migrate dev
npm run seed                    # admin/operator/approver@stellarrail.local / *Pass12345! (dev only)
npm run start:dev               # http://localhost:3000/docs (Swagger)
```

Useful: `npm test` (unit), `CHAIN_DRY_RUN=true npm run test:e2e`
(don't hit chain in tests), `npm run chain:smoke`, `npm run lint`,
`npm run typecheck`. Required env: `DATABASE_URL`, `REDIS_URL`,
`JWT_ACCESS_SECRET`, `JWT_REFRESH_SECRET` (≥ 32 chars), `HORIZON_URL`,
`SOROBAN_RPC_URL`, `ESCROW_CONTRACT_ID`. Chain calls are dry-run unless
explicitly enabled; mainnet needs `CONFIRM_MAINNET=I_UNDERSTAND_REAL_FUNDS`.

### Contracts — `stellarrail-contracts`

```bash
make build        # stellar contract build → escrow.wasm (~20 KiB)
make test         # cargo test --all-targets
make lint         # cargo clippy --all-targets -- -D warnings
SKIP_DEPLOY=1 make deploy-local   # build-only sandbox check
```

Testnet deploys use `./scripts/deploy-testnet.sh` (needs
`SOURCE_SECRET`/`ADMIN_SECRET` — never commit). Live testnet escrow:
`CDAV32AHVV6Q7FFNPUFA76AARTGWBE2QFIU3WAPDV64QIBRTGUFKLU2K`.
See `docs/LOCAL_DEV.md` → `docs/DEPLOY_TESTNET.md` →
`docs/DEPLOY_MAINNET.md` (gated).

### Web — `stellarrail-web`

```bash
cp .env.example .env.local
npm ci --no-audit --no-fund
NEXT_PUBLIC_USE_MOCK=true npm run dev   # MSW mocks, no backend needed
```

Demo: `operator/approver/admin@stellarrail.test` / `password123` /
MFA `123456`. Key env: `NEXT_PUBLIC_API_URL`, `NEXT_PUBLIC_STELLAR_NETWORK`,
`NEXT_PUBLIC_HORIZON_URL`, `NEXT_PUBLIC_SOROBAN_RPC_URL`,
`NEXT_PUBLIC_USE_MOCK`. Checks: `npm run lint`, `npm run typecheck`,
`npm run test:coverage`, `npx playwright test` (e2e, mock mode).

## Cross-repo Testnet flow

1. Contracts: verify testnet deployment (`deployments/testnet.json`) or
   run `scripts/smoke-testnet.sh` (1-XLM deposit → release + deposit → expire).
2. API: set `STELLAR_NETWORK=testnet`, `ESCROW_CONTRACT_ID=CDAV32…2K`,
   `CHAIN_DRY_RUN=false`, then `npm run chain:smoke`.
3. Web: point `NEXT_PUBLIC_API_URL` at the API, `NEXT_PUBLIC_USE_MOCK=false`,
   log in as operator → create payment → approve as approver.

## Troubleshooting

- DB/Redis ports in use → `docker compose down` or use
  `docker-compose.test.yml` ports (5433/6380) for tests.
- Web OOM on build → `NODE_OPTIONS="--max-old-space-size=4096" npm run build`.
- Peer-deps conflict → `npm install --legacy-peer-deps` (web).
- Never run `dev`/`start` in CI; use `build` + `start`.
- Hooks: `npm run prepare` (husky) in api/web; `scripts/install-hooks.sh`
  in contracts.
