# Deployment report — NOT COMPLETE

Date: 2026-09-14. PASS means observed evidence supports that narrow criterion. FAIL includes blocked/not tested and does not imply an executed test failed.

## Current state

LibreChat v0.8.7 is cloned and configured. All four pinned images are pulled. After the Mac was unlocked, Meilisearch is Running; MongoDB, app and admin remain Created. No active host ports. Intended ports: 127.0.0.1:3080 and 127.0.0.1:3000. No account, conversations or working AI provider yet. OpenAI alone is selected but remains `user_provided`.

Docker Desktop responds through the user-local socket. After the user unlocked the Mac, direct UI inspection showed the Docker Subscription Service Agreement awaiting acceptance. User review/acceptance has been requested. Host-folder sharing was also pending in prior host logs; its remaining status is not yet proven. Do not bypass OS/Desktop approvals.

## Acceptance matrix

| Criterion | Result | Evidence / limitation |
|---|---|---|
| OS/CPU detection | PASS | macOS 26.6.2, Apple arm64. |
| Git available | PASS | Git 2.50.1. |
| Docker / Compose installed | PASS | Installed official Apple Silicon Docker Desktop; signature verified. Docker 29.8.0; Compose 5.5.1. CLI requires bundled path until Desktop setup finishes. |
| Docker daemon responds | PASS | Server 29.8.0 responds at user-local Unix socket; this does not prove container startup. |
| Disk and memory checked | PASS | 8 GiB physical RAM; 224,107,343,872 bytes free at initial preflight. vm_stat collected; memory compression present. |
| Ports 3080/3000 free before installation | PASS | lsof found no listeners. |
| Existing installation inventory | PASS | No prior LibreChat directory found in scanned Documents/Projects/Developer and home-name candidates. Docker absent initially; scope was not a full-disk forensic search. Existing containers were not overwritten. Current inventory recorded separately. |
| Operations repository available locally/remotely | PASS | Clean main checkout; private GitHub repository verified using gh and git ls-remote. |
| Pinned non-RC source | PASS | v0.8.7 / 9e74cc0e57b395926122bd4062c1fcedc48ed465; newer observed tags are RCs. |
| Upstream Compose unchanged | PASS | git diff --exit-code -- docker-compose.yml. |
| Exact pulled images and digests | PASS | Four ARM64 images pulled and independently inspected through Docker API; see IMAGE-INVENTORY.json. None running at capture. |
| Required containers healthy/running | FAIL | Meilisearch Running; MongoDB/app/admin Created. Docker service agreement visibly awaits user acceptance; startup remains incomplete. |
| LibreChat opens on localhost | FAIL | HTTP connection refused. |
| Admin opens only on localhost | FAIL | HTTP connection refused. Resolved binding is localhost, but runtime UI untested. |
| First account can be created | FAIL | Not tested: stack unavailable. |
| First single-tenant account has admin access | FAIL | Not tested. Source has first-user ADMIN logic; source inspection is not acceptance. |
| Normal AI conversation completes | FAIL | Not tested; no provider API credential configured. |
| Second independent conversation | FAIL | Not tested. |
| Conversation search | FAIL | Not tested. |
| Project creation | FAIL | Not tested. |
| Conversation assigned to Project | FAIL | Not tested. |
| Container restart preserves conversations | FAIL | Not tested. |
| Stack restart preserves login/user/database | FAIL | Not tested. |
| Logs contain no unexplained critical errors | FAIL | Application containers have not started; no application log evidence. Host logs show pending folder sharing; root cause needs unlocked Desktop inspection. |
| No database/search/vector host publication | PASS | Resolved Compose has no database/search host ports; RAG/vector excluded. Current Docker API reports no active published ports. |
| Localhost-only configured web ports | PASS | Compose JSON independently asserted api 127.0.0.1:3080 and admin 127.0.0.1:3000, one binding each. |
| Secret files unstaged/untracked | PASS | Local populated .env mode 600 and ignored; independently compared against staged file contents before commit. |
| Registration closed after bootstrap | FAIL | Registration is already disabled, but no administrator has been created or rejection tested. |
| GitHub Skill Sync: initial sync and visibility | FAIL | Disabled; baseline gate unmet and fine-grained read-only credential absent. |
| GitHub Skill Sync: execution | FAIL | Not tested; no live provider credential. |
| GitHub Skill Sync: GitHub update reflected | FAIL | Not tested. |
| Agents distinguishable | FAIL | Not created; baseline/Skill Sync gate unmet. |
| Agent Skill/tool restriction | FAIL | Not tested; Agents disabled. |
| General chat usable without Agent | FAIL | Not tested. |
| Agents avoid routing complexity | FAIL | Not tested; two planned specifications only. |
| User can explain each Agent | FAIL | Not validated with user. |
| Backup procedure covers required state | PASS | BACKUP.md covers native Mongo dump, config/secrets, persistent files, Git bundle and image inventory. |
| Verified operational backup | FAIL | No running database or backup archive yet. |
| Disposable restoration test | FAIL | Not performed. |
| Optional stateless interpreter write/contents/hash/download/reset/failure tests | FAIL | Intentionally not implemented: Phases 1–3 have not passed. |
| Stateful evaluation document | PASS | Evaluation only; no backend or worker deployed; explicit later approval required. |

## Security and capability findings

No public exposure configured; no host Docker socket attached to LibreChat. Fresh cryptographic secrets exist only in private local .env. Existing broad gh OAuth credentials were not copied to LibreChat. Registration disabled; sharing disabled. Agents, Skill Sync, MCP, memory, code execution and retrieval are disabled/excluded. No scheduled chats, attached workers, programmatic tools, autonomous Git writes or migration configured. Runtime capability enforcement remains untested.

MongoDB uses upstream internal-network noauth; it is not host-published. Other containers on that network can reach it: never attach unrelated/untrusted containers. Admin image was resolved from upstream latest to an immutable digest; its compatibility still requires runtime testing. Eight GB host RAM is a capacity constraint to observe during testing, not a proven failure.

## Next actions and rollback

1. Review/accept the visible Docker Desktop service agreement, then resolve any remaining startup/folder prompts. Continue startup and verify all four services plus log review.
2. Bootstrap local administrator, close/verify registration, privately configure an existing provider API key, and execute every baseline test. Do not send secrets through chat.
3. Only after baseline passes, provision scoped read-only GitHub Skill Sync credential and run its full update test; then create at most the two documented Agents.
4. Back up and restore-test before declaring acceptance. Optional stateless code follows Phases 1–3; stateful remains evaluation-only.

Rollback: `docker compose stop` in the pilot checkout preserves data. If PATH/socket setup remains incomplete, prepend `PATH=/Applications/Docker.app/Contents/Resources/bin:$PATH DOCKER_HOST=unix:///Users/magnusshouse/.docker/run/docker.sock` and invoke `/Applications/Docker.app/Contents/Resources/cli-plugins/docker-compose stop`. No volumes should be deleted. See RECOVERY.md for upgrade rollback. A start request was pending at the recorded snapshot; recheck actual state after Desktop approvals rather than assuming it remained Created.

Official sources: https://github.com/danny-avila/LibreChat/releases and https://docs.docker.com/desktop/setup/install/mac-install/ . Exact pulled image IDs, RepoDigests and architecture manifests are in IMAGE-INVENTORY.json.
