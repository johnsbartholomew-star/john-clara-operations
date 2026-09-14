# Architecture

ChatGPT: planning, personal context and command center.
GitHub: canonical code, Skills, architecture decisions and runbooks.
Codex: implementation, tests, maintenance and upgrades.
LibreChat: replaceable conversation/interface and later conservative orchestration.

Conversations, Agents, synced Skills and execution workspaces never become the sole copy of important project state. Promote meaningful changes to Git/GitHub promptly. Keep credentials and personal conversation/database backups outside Git.

## Baseline

Four intended services: app, admin, MongoDB and Meilisearch. Host publication must be exactly 127.0.0.1:3080 and 127.0.0.1:3000. MongoDB and search have no host ports. RAG/vector services are excluded with a profile because retrieval is unnecessary for the baseline. The inherited RAG URL is blanked. All interfaces and port checks require runtime verification.

Upstream docker-compose.yml remains untouched. App image uses v0.8.7 plus immutable digest. Admin has an immutable digest resolved from the upstream reference, rather than a floating runtime tag. Mongo/search are also pinned to registry-resolved immutable digests. Actual pulled/running image IDs still require runtime verification. Eight GB host RAM warrants watching memory pressure; no optional code services are deployed.

## Agent specifications — planned, not created

1. John Command Center: routes technical/project work and uses only approved repository Skills. No shell, MCP, autonomous Git writes or execution environment. One-sentence explanation: “It helps me route work using the repository's approved procedures.”
2. Builder / Codex Handoff: turns requirements into an implementation brief with repository, scope, acceptance checks and rollback. No execution/tools by default. One-sentence explanation: “It turns a discussion into a precise task for Codex.”

After Phase 1 and Skill Sync pass, create at most these two and record actual IDs, provider/model, instructions, allowed Skills/tools and screenshots/exports in this directory without credentials. Test distinct behavior, restricted access and ordinary non-Agent chat. More Agents require architecture reassessment.

Disabled: MCP, automatic memory, public sharing, stateful sessions, attached workers, scheduled chats, programmatic tool calling, Code Interpreter, autonomous GitHub writes, history migration. Agents are disabled during baseline. No stateful environment approval is inferred.
