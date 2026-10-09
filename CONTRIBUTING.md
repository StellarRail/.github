# Contributing to StellarRail

Thank you for contributing. This file is the organization default for all
StellarRail repositories. Repo-specific notes (if any) live in each repo's
own `CONTRIBUTING` or `docs/LOCAL_DEV.md` — this file takes precedence on
process, the repo takes precedence on commands.

## Code of conduct

By participating you agree to our
[Code of Conduct](./CODE_OF_CONDUCT.md). Report issues to the maintainers
listed in [SUPPORT.md](./SUPPORT.md).

## What to work on

- Good first issues are labeled `good first issue` in each repo.
- Bugs: use the bug-report template. Include repro steps, expected vs.
  actual, commit SHA, network (`testnet` / `mainnet` / `standalone`).
- Features: use the feature-request template. Contract interface changes
  (`initialize`, `deposit`, `release`, `refund`, `expire`, views, admin
  entrypoints) are **frozen at v1** — they require an ADR in
  `stellarrail-contracts/docs/adr/` and maintainer approval before code.
- Security issues: **do not open a public issue** — see [SECURITY.md](./SECURITY.md).

## Workflow

1. Fork the repo, create a topic branch from `main`:
   `git checkout -b feat/short-description` (or `fix/…`, `docs/…`, `chore/…`).
2. Follow Conventional Commits (`feat:`, `fix:`, `docs:`, `ci:`, `chore:`,
   `test:`, `refactor:`). API and web enforce this with commitlint/husky.
3. Keep PRs small and focused. One logical change per PR.
4. Update docs + changelog-adjacent notes when behavior changes:
   - API: `docs/*.md` + Swagger annotations.
   - Contracts: `docs/*.md` + `docs/adr/` if interface-adjacent.
   - Web: `README.md` + route/feature docs if UX changes.
5. Open a PR against `main` using the PR template. Link the issue
   (`Fixes #NN`).

## Definition of done

- [ ] `npm run lint` / `cargo clippy -- -D warnings` clean (per repo).
- [ ] `npm run typecheck` / `cargo fmt --check` clean.
- [ ] Tests added or updated; full suite green:
      `stellarrail-api`: `npm test` + `npm run test:e2e:ci`
      (`CHAIN_DRY_RUN=true`); `stellarrail-contracts`: `cargo test --all-targets`;
      `stellarrail-web`: `npm run test:coverage` (+ Playwright for UX changes).
- [ ] No secrets committed (gitleaks runs in CI). No `.env` files, no
  `SOURCE_SECRET` / `ADMIN_SECRET` / JWT secrets in diffs.
- [ ] Docs updated. Screenshots for web UI changes.

## Local setup (pointers)

| Repo | Toolchain | Quick loop |
|---|---|---|
| `stellarrail-api` | Node 20, Docker (Postgres 15, Redis 7) | `cp .env.example .env && docker compose up -d && npm install && npx prisma migrate dev && npm run seed && npm run start:dev` |
| `stellarrail-contracts` | Rust 1.97.1, `stellar-cli` ≥ 27.1.0 | `make build && make test && make lint`; sandbox via `SKIP_DEPLOY=1 make deploy-local` |
| `stellarrail-web` | Node 20 | `cp .env.example .env.local && npm ci && NEXT_PUBLIC_USE_MOCK=true npm run dev` |

Full cross-repo flow is in [`docs/DEVELOPMENT.md`](./docs/DEVELOPMENT.md).

## Review process

- All PRs need **1 maintainer approval** and green CI.
- `main` is protected in every repo. Direct pushes are disabled.
- Maintainers may request changes on scope, tests, or docs. Stale PRs
  (no response in 14 days) may be closed with a note.

## Releases

Releases are cut by maintainers — see [`docs/RELEASING.md`](./docs/RELEASING.md).
Do not push `v*` tags from forks.

## License

By contributing you agree your contributions are licensed under the
receiving repo's license (API: Apache-2.0; contracts: MIT OR Apache-2.0;
web: MIT; `.github`: Apache-2.0).
