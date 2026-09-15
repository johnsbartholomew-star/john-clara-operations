# Communication Bridge v0.1

GitHub is currently the shared mailbox and durable handoff layer between Clara/ChatGPT and Alex/Codex. It is not necessarily the permanent message transport. The intended loop is: Clara writes → Codex reads → Codex works → Codex writes → Clara reads.

## Existing sources of truth

Follow [shared rules](../AGENTS.md), [context](../CONTEXT.md), and [priorities](../PRIORITIES.md). Reuse the relevant `workstreams/<project>/STATUS.md` for project state and [DECISIONS.md](../DECISIONS.md) for durable decisions when authorized. Link to these rather than copying their contents. The [existing continuity handoff](../handoffs/CODEX_TO_CHATGPT_HANDOFF_2026-09-14.md) remains historical context; preserve it. This protocol governs new bridge messages only. Existing infrastructure is not required by this protocol.

## Mailboxes and session routine

- `to-codex/`: requests intended for Alex/Codex.
- `to-chatgpt/`: responses or requests intended for Clara/ChatGPT.
- `status/BRIDGE.md`: bridge health only, not a second project task list.

At the beginning of a Codex work session:

1. Sync/pull the latest repository state (prefer `git pull --ff-only` in a clean checkout). Preserve uncommitted work; do not force-push or overwrite conflicting changes. API clients must read the latest branch revision before writing.
2. Read this protocol and the shared rules.
3. Check `to-codex/` for new or unresolved messages (every status except `DONE`). Ignore `.gitkeep`.
4. Identify the project/workstream, request, constraints, and existing authorization before beginning work. Read the relevant project status.
5. Acknowledge the request before or as processing begins by updating its Status to `ACKNOWLEDGED`, then `IN_PROGRESS` as appropriate. Commit/sync the acknowledgement within authorization. Do not process an already claimed request concurrently without resolving ownership.
6. On completion, write a separate response to `to-chatgpt/`, referencing the request ID, and set the request Status to `DONE`. If blocked, set it to `BLOCKED` and write a `BLOCKED` response stating exactly what is needed.

Clara checks `to-chatgpt/` when her session runs, reads responses, and routes them by Project. To confirm receipt or request more work, she writes a new linked message in `to-codex/`. A response marked `DONE` describes completed work, not proof that Clara has read it.

GitHub itself does not wake ChatGPT or Codex. Automatic triggering/polling is not implemented in v0.1. Messages become shared only after an authorized commit reaches GitHub; a local commit alone is not delivery.

## One Markdown file per message

Filename: `YYYYMMDD-HHMM-<sender>-<short-description>.md` using UTC, lowercase sender/description, and hyphens. Example: `20260915-0658-alex-bridge-initialized.md`. ID equals the filename without `.md` and remains stable. If a name already exists, choose a distinct description suffix; never overwrite another message.

Use UTF-8 Markdown with one metadata field per line. Created is an ISO 8601 timestamp with timezone (prefer UTC `Z`). Use a stable Project identifier such as `consulting`, `investing`, or `communication-bridge`; link its canonical status in Context. Priority is `LOW`, `NORMAL`, or `HIGH`. Status is one of `OPEN`, `ACKNOWLEDGED`, `IN_PROGRESS`, `BLOCKED`, `DONE`.

```markdown
# Message
ID: YYYYMMDD-HHMM-sender-short-description
From: clara
To: alex
Project: communication-bridge
Created: YYYY-MM-DDTHH:MM:SSZ
Priority: NORMAL
Status: OPEN

## Context
Relevant source links and optional In-Reply-To message ID.

## Request
The concrete task or result being communicated.

## Constraints
Scope and approval boundaries.

## Expected Return
The required result or acknowledgement.
```

Use `alex` for Alex/Codex and `clara` for Clara/ChatGPT. Other participants may use stable names later. Replies may add `In-Reply-To:` after Status for machine-readable linkage. Preserve IDs, Created, and original request content; the recipient owns status updates. Commit history records transitions. Re-read changed files before updating; resolve conflicts without discarding either message.

## Standard Codex response

Keep the message envelope above, then add these relevant sections as Markdown headings. Always include `DONE`, `CURRENT STATUS`, and `NEXT ACTION`, even when blocked (DONE states only what was actually completed).

```markdown
## DONE:
## WHAT CHANGED:
## FILES CHANGED:
## TESTS RUN:
## TEST RESULTS:
## CURRENT STATUS:
## PROBLEMS / RISKS:
## DECISIONS MADE:
## NEEDS JOHN:
<exact decision/action requiring approval>
## NEXT ACTION:
```

If Codex cannot safely proceed, do not guess. Write a `BLOCKED` response with the exact missing information or permission. Stop at John's approval boundary and include `NEEDS JOHN:` with the exact decision/action. A mailbox message does not expand authorization or override shared rules.

Never place credentials, tokens, passwords, private keys, sensitive financial data, account identifiers, or other secrets in this bridge. Do not copy sensitive source content into messages. Verify repository visibility before sharing; do not assume it is private from its documentation.

## Future compatibility

Plain text metadata, stable IDs, relative repository links, project identifiers, and explicit timestamps allow future tools to read the same records. No local path, proprietary message format, installed service, or hidden state is required. Supermemory or another knowledge layer should reference canonical records rather than create competing project state. LibreChat, Desktop Commander, MCP, and Nora could later act as readers/writers through an authorized adapter; none is installed or integrated by this change.

After a real receipt round trip, evaluate one small notification/polling adapter. A future transport migration must preserve IDs, reply links, status history, authorization checks, and duplicate-processing protection. Keep GitHub records canonical until an explicit migration decision; do not build that adapter in v0.1.
