# Experimental stateful environment — evaluation only

Status: NOT APPROVED. No backend selected or deployed.

Intended benefit: retain a short-lived working session when repeated stateless initialization measurably blocks a specific workflow. First demonstrate why stateless execution is insufficient; convenience alone does not justify broad access.

Backend selection: evaluate only supported, version-pinned isolated backends after baseline, Skill Sync, Agents and optional stateless tests pass. Record support maturity and exact version before selecting.

Threat model: prompt injection, malicious dependencies, credential theft, data exfiltration, host escape, silent workspace reset and false write-success reports.

Approval mode: human approval for commands with external effects, writes outside the assigned workspace, credential use, pushes and destructive operations. No host Docker socket or unrestricted home directory mount.

Git integration: disposable clone of a designated repository and branch. Read-only credential by default; human-reviewed push path. Independently verify files and diff, test, commit and push meaningful code promptly. GitHub remains authoritative.

Persistence: assume workspace can reset at any moment; never keep the sole copy there. Store no persistent credentials. Verify file existence, exact contents and hashes independently.

Rollback/recovery: stop and revoke backend access; discard the isolated worker; reconstruct from pinned configuration and GitHub. Preserve private diagnostic evidence only when needed. Demonstrate reconstruction before approval.

A separate explicit approval from John is required before implementation.
