# Security Policy

StellarRail moves real value on Stellar. Security reports are taken
seriously and handled privately.

## Reporting a vulnerability

**Do not open a public GitHub issue for security reports.**

- Email: **security@stellarrail.example** (see also
  `stellarrail-contracts/SECURITY.md` for contract-specific scope).
- Include: affected repo + commit/tag, impact, step-by-step repro or PoC,
  Stellar network (testnet/mainnet/standalone), and your contact + PGP key
  if you want encrypted replies.
- You will receive an acknowledgement within **48 hours** and a triage
  update within **5 business days**.

We follow coordinated disclosure: please give us a reasonable window
(standard **90 days**, negotiable for critical on-chain issues) before any
public disclosure, and do not access other users' funds or data beyond what
is necessary to demonstrate the issue.

Safe harbor: good-faith research that stays within this policy and avoids
privacy violations, service disruption, or fund movement will not be
referred to law enforcement.

## Supported versions

| Repo | Supported tags | Notes |
|---|---|---|
| `stellarrail-api` | `1.0.0-rc1` + `main` | Pre-GA. Security fixes land on `main` and are tagged as `-rc`/`-patch`. |
| `stellarrail-contracts` | `contracts-v1.0.0-rc1` / `main` (testnet-only) | Interface frozen at v1. **No mainnet** without an external audit. See `docs/AUDIT_CHECKLIST.md`. |
| `stellarrail-web` | `1.0.0-rc1` + `main` | Pre-GA. |
| `.github` | `main` | Docs/defaults only. |

Only the latest RC/patch on `main` is supported. Older tags are
unsupported — upgrade.

## Out of scope (for bounty-style reports)

- Mainnet contract addresses that are still marked `pilot-pending`.
- Third-party infrastructure (Stellar Horizon/RPC, AWS, GitHub Actions
  runners) — report to the upstream vendor, but tell us if we misconfigure it.
- Social engineering, physical attacks, or `example.com` placeholder
  infrastructure in docs.

## What we do with reports

1. Triage severity (critical = loss/theft of funds, key extraction,
   auth bypass, SoD bypass; high = audit-log tampering, reconciliation
   bypass; medium/low = hardening).
2. Fix privately, add regression tests (API: Jest; contracts:
   `cargo test --all-targets` incl. `auth_matrix`; web: Vitest/Playwright),
   run `gitleaks`, `cargo audit` / `npm audit`, Trivy/CodeQL where configured.
3. Publish a GitHub Security Advisory, credit the reporter (unless they
   opt out), and tag a patched release.

## Hardening already in place

- KMS/Vault-backed signing; no raw keys in env; `CONFIRM_MAINNET` gate;
  `READ_ONLY_MODE` for degraded RPC.
- Contract: `require_auth` on admin/signer paths, permissionless `expire`
  only, paused-state guards, checked arithmetic, 2-step admin rotation.
- API: Helmet, throttling, Zod/class-validator input validation, SoD
  enforcement (`operator != approver`), idempotency keys, hash-chained
  audit logs.

See `stellarrail-api/docs/SECURITY_REVIEW.md` and
`stellarrail-contracts/SECURITY.md` for the full threat models.
