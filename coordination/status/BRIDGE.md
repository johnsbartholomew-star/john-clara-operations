# Bridge status

Bridge version: v0.1
State: TESTING
ChatGPT side: Public repository read and Clara receipt confirmed; repository write BLOCKED (HTTP 403).
Codex side: Git read, commit, push, and remote readback verified through Alex's local Git connection. This does not verify Clara's connector write access.
Automatic wake/triggering: NOT IMPLEMENTED
Last successful communication test: Clara independently retrieved the protocol, bridge status, and Alex's messages, as reported by John on 2026-09-15. Alex → GitHub → Clara is confirmed. Clara's exact test time was not supplied.
Known limitations: Clara → GitHub → Alex remains blocked/unverified pending proper ChatGPT GitHub authorization. Repository is public. No automatic wake, polling, or receipt detection exists.
Next architecture improvement: Resolve the supported GitHub authorization and complete the first full round trip before considering notifications or a new transport.
Updated: 2026-09-15T07:15:37Z

## Evidence

- Alex pushed scaffold commit `a18fa3888c6e0ea08a28a216752645fa6df02b73` and [delivery confirmation](../to-chatgpt/20260915-0703-alex-bridge-delivery-confirmed.md) in commit `30088f7ef6f95f2cb3d7a73906b50b9ecc4fd1fd`; GitHub API readback matched local files.
- John relayed Clara's independent test: she read the protocol, bridge status, and Alex's messages directly from GitHub, without John copying message contents. This confirms Alex → GitHub → Clara.
- Clara's acknowledgement creation in `to-codex/` returned HTTP 403; a second lower-level Git write also returned HTTP 403 (Clara's results relayed by John, not reproduced by Alex).
- Alex's live GitHub connector checks recognize the repository owner's login but return `installations: []` and `accounts: []`. This is separate from the working local Git connection. No Clara acknowledgement exists in the current remote mailbox.
- This supersedes the earlier task-supplied claim that ChatGPT read/write was verified. Read/receipt is confirmed; write is not.

## Root cause and authorization path

The observed authorization gap is a missing GitHub App installation/repository grant visible to the ChatGPT connector, despite a recognized user login. This is consistent with both reported 403 failures; the exact server-side denial was not independently diagnosed from raw error responses. A successful post-authorization write is required before declaring the blocker resolved.

GitHub distinguishes user authorization from app installation: either can exist without the other. Its installation flow allows selecting individual repositories. Use the official app setup opened by ChatGPT's existing GitHub connection and select only this repository. Do not assume a separate Codex connection authorizes Clara. [GitHub installation documentation](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party).

For this Markdown-file test, the required repository capability is Contents write (with read access for retrieval). It does not require workflow editing. The app determines its requested permission bundle; John may be able to limit repository selection without individually editing every permission. Review the offered bundle before accepting; repository selection is not a restriction to the `coordination/` folder. [GitHub contents API](https://docs.github.com/en/rest/repos/contents?apiVersion=2022-11-28).

## Minimum action for John

1. In the ChatGPT account/workspace Clara uses, open **Plugins → GitHub** and its connection/setup controls. Complete the official GitHub connection or reconnect flow if prompted. Control labels can vary; the exact account UI has not been inspected. [OpenAI plugin setup](https://learn.chatgpt.com/docs/plugins).
2. On GitHub, choose **johnsbartholomew-star → Only select repositories → john-clara-operations**. Review the requested permissions, including repository contents write, then complete **Install/Authorize** as offered. If the app is already installed, use its repository configuration instead of creating a second integration.
3. Return to Clara and retry the single acknowledgement test below. Verify the installation/account and selected repository are now visible. If tools have not refreshed, start a new chat using the same GitHub plugin and point it at this status file.

If the offered connection is read-only or does not expose the required app setup, keep the bridge BLOCKED and report the exact non-sensitive screen/error. Broader repository access cannot create a capability the app does not support. If an installation appears but writing still fails, inspect its contents permission and applicable ChatGPT action controls/repository rules before another attempt; do not bypass those controls. [OpenAI connection and action controls](https://learn.chatgpt.com/docs/enterprise/apps-and-connectors).

## Next test

1. Clara creates one acknowledgement in `coordination/to-codex/`, with `Status: OPEN`, `Project: communication-bridge`, and `In-Reply-To: 20260915-0703-alex-bridge-delivery-confirmed`, following the protocol.
2. Alex syncs, reads and acknowledges that actual file, then writes a linked confirmation to `coordination/to-chatgpt/`.
3. Clara retrieves Alex's confirmation and confirms that retrieval. Only then record the first full round trip as PASS.

No authentication settings, credentials, integrations, or unrelated project files were changed during this follow-up. Session starts remain manual.
