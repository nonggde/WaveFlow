# Contributing to WaveFlow

WaveFlow automates bounty escrow on Stellar/Soroban with GitHub merge-triggered payouts. It is designed for Stellar-native bounty programs where maintainers fund escrow, contributors submit PRs, and verified merges trigger wallet-direct rewards.

## Before you start

1. Read [docs/PRD.md](docs/PRD.md) for product scope and launch gates.
2. Read [docs/ROADMAP.md](docs/ROADMAP.md) for phase order and issue mapping.
3. Walk through [docs/core-loop.md](docs/core-loop.md) for the merge-to-payout path.
4. Review [AGENTS.md](AGENTS.md) for repo-specific agent and automation guidance.

## Stellar-native Wave alignment

WaveFlow follows Drips Wave-style contribution mechanics, but the implementation is independent and Stellar-native. Per PRD OQ6, v1 has no Drips API dependency: the gateway verifies GitHub events, records the audit trail, and submits an authorized Soroban attestation for escrow payout.

Contributors should keep PRs aligned with this model:

- GitHub issues describe the work and acceptance criteria.
- Maintainers use labels to communicate area and complexity.
- Merged PRs are the trigger for reward accounting.
- Wallet payouts are handled through Stellar/Soroban, not through manual secret-key handling or off-chain treasury scripts.

## Issue prefixes and labels

Use an issue title prefix to show the primary area touched. If a PR touches multiple areas, pick the prefix for the user-visible outcome and mention supporting changes in the PR body.

| Prefix | Area | Typical paths |
|--------|------|---------------|
| `[contracts]` | Soroban escrow contract logic | `contracts/waveflow-escrow` |
| `[gateway]` | GitHub webhook ingestion and chain attestation | `crates/gateway` |
| `[api]` | REST read paths, admin routes, and auth | `crates/api` |
| `[shared]` | Shared types, config, and error taxonomy | `crates/shared` |
| `[database]` | Postgres schema and migrations | `migrations` |
| `[infra]` | Docker, Render, runtime config, and deployment | `Dockerfile.*`, `docker-compose.yml`, `render.yaml` |
| `[documentation]` | Contributor, operator, and product docs | `README.md`, `docs`, `CONTRIBUTING.md` |
| `[security]` | Secrets, HMAC, replay protection, and runbooks | `docs/security*`, gateway verification code |

Wave bounty issues should also use:

- `Drips Wave` for work aligned with the Wave contribution program.
- `complexity:low`, `complexity:medium`, or `complexity:high` for reward/point mapping.
- Area labels such as `gateway`, `api`, `contracts`, `infra`, or `documentation` when available.

## Claiming and working an issue

1. Pick an open issue with clear acceptance criteria.
2. Check for existing linked PRs before starting.
3. Comment if the maintainer asks for assignment before work begins.
4. Keep the change narrow enough that it can be reviewed against one issue.
5. Reference the issue in the PR body with `Closes #<issue-number>` when the PR fully satisfies it.

## Development setup

```bash
git checkout main && git pull
git checkout -b feature/your-change
cp .env.example .env
docker-compose up -d
cargo build --workspace
cargo test --workspace
```

## Workspace layout

| Crate / path | Role |
|--------------|------|
| `contracts/waveflow-escrow` | Soroban escrow contract |
| `crates/gateway` | GitHub webhooks and chain attestation |
| `crates/api` | REST API for programs and payouts |
| `crates/shared` | Config, types, errors |
| `docs/PRD.md` | Product requirements and non-goals |
| `docs/ROADMAP.md` | Phase plan and launch gates |
| `docs/core-loop.md` | Merge-to-payout walkthrough |

## Pull requests

- Branch from `main`, open PRs against `main`.
- Prefer branch names such as `gateway/hmac-replay-window`, `api/program-payouts`, or `docs/contributing-wave-labels`.
- Keep PRs focused on one issue or launch-gate item.
- Include a short summary, validation steps, and any skipped checks.
- Run `cargo fmt --all -- --check`, `cargo clippy --all-targets --all-features -- -D warnings`, and `cargo test --workspace` before pushing when code changes are included.
- For documentation-only PRs, check links and keep examples consistent with the PRD and core-loop docs.

## Security

Never commit real webhook secrets, API admin keys, RPC credentials, or Stellar secret keys. Use `.env.example` for placeholders and see [docs/security-checklist.md](docs/security-checklist.md) before touching gateway verification, signing, or payout code.
