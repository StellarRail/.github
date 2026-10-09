# Support

## Where to ask

| Channel | Use for | Response target |
|---|---|---|
| [stellarrail-api issues](https://github.com/StellarRail/stellarrail-api/issues) | API bugs, auth/payments/reconciliation questions | 2 business days |
| [stellarrail-contracts issues](https://github.com/StellarRail/stellarrail-contracts/issues) | Contract behavior, deployments, toolchain | 2 business days |
| [stellarrail-web issues](https://github.com/StellarRail/stellarrail-web/issues) | Dashboard bugs, UX, mock mode | 2 business days |
| [GitHub Discussions](https://github.com/orgs/StellarRail/discussions) (if enabled) | Design questions, ideas | Best effort |
| `security@stellarrail.example` | **Private** security reports only | 48 h acknowledgement |

Before opening an issue, search existing issues and check the repo docs:

- API: `stellarrail-api/docs/{API,RUNBOOK,CHAIN_RUNBOOK,DEPLOY}.md`,
  Swagger at `/docs`.
- Contracts: `stellarrail-contracts/docs/{LOCAL_DEV,DEPLOY_TESTNET,DEPLOY_MAINNET,ERRORS}.md`.
- Web: `stellarrail-web/README.md` + `docs/PRODUCTION.md`.
- Org-wide: this repo's [`docs/`](./docs/).

## Bug reports

Use the bug-report template and include:

- Repo + commit SHA (or tag), environment (`dev` / `staging` / `prod`),
  Stellar network (`testnet` / `mainnet` / `standalone`).
- Repro steps, expected vs. actual, logs (redact secrets!), screenshots
  for web issues.

## Operational incidents

For staging/prod incidents, follow the runbooks first:

1. `stellarrail-api/docs/RUNBOOK.md` (5xx, queues, DB, key rotation).
2. `stellarrail-api/docs/CHAIN_RUNBOOK.md` (RPC down, retries).
3. `stellarrail-contracts/docs/DISASTER_RECOVERY.md` (backend-down
   direct-CLI settlement).

Then open an issue with the `incident` label and link the
reconciliation-run ID if applicable.

## SLAs

This is a pre-GA pilot (`1.0.0-rc1`). Support is best-effort outside
critical security issues. There is no paid SLA yet.

## Contacts

- Maintainers: `@StellarRail/core` (CODEOWNERS in each repo).
- Conduct reports: see [CODE_OF_CONDUCT.md](./CODE_OF_CONDUCT.md).
- Security: see [SECURITY.md](./SECURITY.md).
