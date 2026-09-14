# Local LibreChat pilot — NOT ACCEPTED

GitHub is the permanent source of truth. LibreChat is replaceable UI/orchestration.

Pinned source: `v0.8.7`, commit `9e74cc0e57b395926122bd4062c1fcedc48ed465`.
Official repository: https://github.com/danny-avila/LibreChat.git
Release investigation on 2026-09-14 found v0.8.8 RC tags; retain v0.8.7.

See DEPLOYMENT-REPORT.md for evidence and blockers. Files here are prepared configuration, not proof of a running deployment. Do not start later phases until baseline authentication and persistence pass.

## Local setup

Docker Desktop must finish startup and expose a healthy local daemon. Use Docker Compose >=2.24.4 (`!override` replaces inherited port bindings).
Clone upstream at the exact tag in an isolated local directory. Copy the tracked override and librechat.yaml beside upstream Compose. Create `.env` from upstream `.env.example`; generate fresh CREDS_KEY (32 bytes hex), CREDS_IV (16 bytes hex), JWT_SECRET, JWT_REFRESH_SECRET, MEILI_MASTER_KEY and ADMIN_PANEL_SESSION_SECRET. Keep `.env` mode 600, ignored and outside this repository.

The prepared deployment currently exists at:
`/Users/magnusshouse/Documents/Codex/2026-09-14/objective-install-and-validate-a-local/LibreChat`

Run `docker compose config --services` and verify only api, admin-panel, mongodb and meilisearch. Inspect resolved port bindings without printing secret environment values. Never use `--profile excluded-rag` or `--profile '*'` with this pilot.
Run `docker compose pull`, record each actual image ID and RepoDigest, replace remaining version-only image references with immutable digests, then `docker compose up -d`.

Registration starts CLOSED. Temporarily enable it during supervised first-account bootstrap, verify the first account's ADMIN role, immediately disable registration and recreate api, then verify another registration is rejected. Keep local account credentials in a password manager.

## Provider action

Only OpenAI is selected through ENDPOINTS=openAI. No working API credential has been found in the process environment. The local `.env` retains `OPENAI_API_KEY=user_provided`; this is not an active provider. Enter an existing API key privately in the application or local .env; never paste credentials into chat or Git. No paid account or API purchase has been made. Do not repurpose Codex/ChatGPT session credentials.

## Skill Sync action — baseline must pass first

Repository access was verified via gh and git; the operations repository is private and uses main. LibreChat sync is prepared but DISABLED. Provision a separate fine-grained PAT restricted to johnsbartholomew-star/john-clara-operations, Contents read and Metadata read. Store as GITHUB_SKILLS_TOKEN in local .env. The existing broad gh OAuth credential is not copied into LibreChat.

After baseline passes, commit a harmless skills/librechat-test/SKILL.md with a unique marker to GitHub, enable sync, verify its visibility and invocation, change the marker in GitHub, sync again and prove the updated marker is invoked. Record both commits and observed results. Disable/remove the test after validation. No success is assumed from a sync log alone.
