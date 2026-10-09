# StellarRail `.github` — Organization Defaults & Profile

This is the [StellarRail](https://github.com/StellarRail) special
[`.github` repository](https://docs.github.com/en/organizations/collaborating-with-groups-in-organizations/customizing-group-blogs).
It serves two purposes:

1. **Organization profile** — `profile/README.md` is rendered on
   https://github.com/StellarRail.
2. **Organization defaults** — community health files and templates
   (`CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`, `SUPPORT.md`,
   `CODEOWNERS`, issue/PR templates) that apply to all StellarRail
   repositories unless a repo overrides them.

## Layout

```text
.
├── README.md                    # this file — repo index
├── profile/README.md            # org profile page
├── CONTRIBUTING.md              # how to contribute (all repos)
├── CODE_OF_CONDUCT.md           # Contributor Covenant 2.1
├── SECURITY.md                  # disclosure policy + supported versions
├── SUPPORT.md                   # where to get help
├── LICENSE                      # Apache-2.0
├── CODEOWNERS                   # default reviewers (also mirrored in .github/)
├── .github/
│   ├── CODEOWNERS
│   ├── PULL_REQUEST_TEMPLATE.md
│   └── ISSUE_TEMPLATE/
│       ├── bug_report.yml
│       ├── feature_request.yml
│       └── config.yml
└── docs/
    ├── ARCHITECTURE.md          # system overview + component diagram
    ├── DEVELOPMENT.md           # local dev across all repos
    ├── RELEASING.md             # versioning + release process
    ├── ROADMAP.md               # v1.0 scope + post-GA plans
    └── GLOSSARY.md              # ubiquitous language
```

## Repositories

| Repository | Description | Stack |
|---|---|---|
| [stellarrail-api](https://github.com/StellarRail/stellarrail-api) | Payment orchestration API, auth/RBAC, escrow orchestration, reconciliation workers | NestJS 10 · Node 20 · Postgres 15 · Redis 7 / BullMQ · `stellar-sdk` v11 |
| [stellarrail-contracts](https://github.com/StellarRail/stellarrail-contracts) | Soroban escrow contract (`deposit` / `release` / `refund` / `expire`) | Rust 1.97.1 · Soroban SDK 28 · `stellar-cli` 27.1.0 |
| [stellarrail-web](https://github.com/StellarRail/stellarrail-web) | Operator / approver / admin dashboard | Next.js 15.5.27 · React 18 · TypeScript 5.4 · TanStack Query 5 · Zustand 4 |

Current release across all repos: **`1.0.0-rc1`** (Testnet pilot).
Testnet escrow contract: `CDAV32AHVV6Q7FFNPUFA76AARTGWBE2QFIU3WAPDV64QIBRTGUFKLU2K`.

## Conventions

- Default branch: `main`. Protected in every repo (CI green + 1 approval).
- Conventional Commits (`commitlint` in api/web). Contract interface changes
  require an ADR in `stellarrail-contracts/docs/adr/`.
- See `CONTRIBUTING.md` for the full workflow and `docs/RELEASING.md`
  for how `*-v*` tags and changelogs work.
