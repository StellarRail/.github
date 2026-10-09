# Releasing StellarRail

## Versioning

- API + web: SemVer (`1.0.0-rc1` now). Contracts: `contracts-v*` tags
  (e.g. `contracts-v1.0.0-rc1`) so release workflows can filter per repo.
- Contract **interface is frozen at v1** (`version() = 1`). Any entrypoint
  change needs an ADR and a major version discussion — see
  `stellarrail-contracts/docs/adr/`.

## Branching

- `main` is protected everywhere: green CI + 1 approval, no direct pushes.
- Feature work on topic branches (`feat/…`, `fix/…`, `docs/…`, `ci/…`).
- Release candidates are tagged from `main` after the gates below.

## Pre-release gates (all must pass)

| Repo | Gate |
|---|---|
| api | `npm run lint`, `typecheck`, `test`, `test:e2e:ci`, `prisma:validate`, Trivy/CodeQL/gitleaks green; `docs/PRODUCTION.md` checklist signed |
| contracts | `cargo fmt --check`, `clippy -D warnings`, `cargo test --all-targets`, `make build` (WASM sha recorded in `deployments/builds.json`), `docs/AUDIT_CHECKLIST.md` + `PRODUCTION.md` gates; mainnet gate skipped by default |
| web | `lint`, `typecheck`, `test:coverage` (thresholds 70/60/60/70), Playwright (`core`, `a11y`, `visual`), `docs/PRODUCTION.md` check |
| org | `profile/README.md` + `docs/ROADMAP.md` version strings bumped together |

## How to cut a release

1. Update `CHANGELOG.md` in the releasing repo(s) (api/contracts/web all
   keep one; current entry `1.0.0-rc1 — 2026-10-05`).
2. Open a PR `chore: release <repo> <version>` with the changelog + version
   bumps (`package.json` / `Cargo.toml` as applicable). Get approval, merge.
3. Tag from updated `main`:
   `git tag stellarrail-api-v1.0.0-rc1 && git push origin <tag>`
   (use `contracts-v*` for contracts, `v*` for web per that repo's
   `release.yml` — check the workflow's tag filter first).
4. GitHub Actions builds Docker images (`ghcr.io`), runs Trivy scans,
   and creates a GitHub Release with notes. Verify image + smoke test
   (api `chain:smoke`, contracts `smoke-testnet.sh`, web preview deploy).

## Mainnet pilot (extra gates)

Mainnet is **not** a normal release — it requires the ceremony in
`stellarrail-contracts/docs/DEPLOY_MAINNET.md` (5 gates incl. external
audit) plus api `CONFIRM_MAINNET=I_UNDERSTAND_REAL_FUNDS` and the
`PRODUCTION.md`/`DISASTER_RECOVERY.md` runbooks. Do not retag a testnet RC
as mainnet-ready.

## Hotfixes

Branch from the release tag (`hotfix/<issue>`), fix + regression test,
PR to `main`, then tag `<version>-patch.N`. Never force-push tags.
