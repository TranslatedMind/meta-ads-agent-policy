# Agent Rules for Official Meta Ads CLI Automation

> Community template. This is a project policy, not an official Meta document.
> To adopt it, save it as `AGENTS.md` in your project and provide an approved local wrapper implementing the requirements below.
> `scripts/meta` and `.meta-profiles/<profile>.env` are example project conventions; adapt these paths consistently if your setup differs. The wrapper and credential files are not included in this document.
> Confirm command names and dry-run support against the installed CLI version using local documentation or help before enabling execution. If the required wrapper or safeguards are missing, limit work to local analysis and command drafting.

These rules apply to all agents, MCP servers, scripts, and local workflows that interact with Meta Ads from this workspace.

Agents may execute Meta Ads operations only by running the official Meta Ads CLI through an approved local wrapper. A narrow read-only browser inspection exception is defined below for reviewing visible Meta App Settings and Business Settings. Do not use unofficial CLIs, scraping, private surfaces, or direct network scripts.

The expected production pattern is:

```text
agent -> explicit isolated profile -> approved local wrapper -> official Meta Ads CLI -> Meta
```

Agents must not bypass the wrapper for convenience.

## Core Principles

1. Use the official Meta Ads CLI as the only executor for Meta Ads operations.
   - Use the `meta ads ...` command family for Meta Ads work.
   - Do not automate Ads Manager through a browser.
   - Read-only browser inspection may be used only under the exception below and must never execute or prepare a Meta Ads mutation.
   - Do not scrape Meta surfaces.
   - Do not use unofficial/private Meta surfaces.
   - Do not write custom direct-call scripts for Meta Ads tasks.

2. Treat the system-user token as a production secret.
   - A token must be provided only as `ACCESS_TOKEN` from the explicitly selected `.meta-profiles/<profile>.env` file.
   - Never pass the token as a command argument.
   - Never paste tokens into chat, prompts, MCP config, logs, shell history, screenshots, issues, PRs, or generated docs.
   - Do not run commands that save credentials into user-level config.
   - If a token is exposed, stop work and ask the human to rotate it.

3. Use the actual system-user token model.
   - Use one system-user `ACCESS_TOKEN` per isolated profile. Each profile represents one Meta app, token, ad account, and optional business. Use separate profiles for separate projects or clients.
   - Never load more than one profile or token into a Meta CLI process.
   - There is no default profile. Every Meta operation must explicitly select one with `scripts/meta --profile <profile> ...`.
   - The token's practical power is determined by the assets assigned to the system user and the permissions/scopes selected in Meta Business Suite.
   - Agents must not assume local read/write token separation exists.
   - All write-capable commands require exact human approval.

4. Default mode is read-only or dry-run.
   - Agents may fetch reports, summarize account state, detect anomalies, and draft recommendations.
   - Agents may draft exact Meta Ads CLI commands.
   - Agents must not create, edit, pause, activate, publish, duplicate, change budgets, change bids, change targeting, assign assets, remove assets, or upload creatives without explicit human approval.

## Read-Only Browser Inspection Exception

An agent may inspect Meta in the user's existing browser session only when the user explicitly asks for read-only review of Meta App Settings or Meta Business Settings.

Allowed browser actions:

- view the currently open page
- navigate between settings pages, tabs, and sections
- scroll and read visible configuration, status, roles, permissions, and asset assignments
- report whether the visible setup appears correct and identify missing configuration

Browser actions must not change Meta state. Agents must not:

- type into fields or forms
- change selectors, checkboxes, toggles, permissions, roles, or assignments
- click Save, Submit, Confirm, Generate Token, Create, Delete, Remove, Publish, or similar controls
- operate Ads Manager or create, edit, pause, activate, duplicate, or publish any ad object
- expose, copy, print, log, or capture App Secret, access tokens, passwords, cookies, authorization data, or recovery codes

Browser inspection is for setup review only. It does not replace the approved wrapper, does not authorize scraping, and does not authorize Ads API operations. If an action may mutate state or its effect is unclear, the agent must stop and ask the human to perform it.

## Environment Configuration

Only these workspace configuration sources are allowed:

- the explicitly selected `.meta-profiles/<profile>.env` file for Meta credentials and account identifiers
- exported shell environment for non-credential wrapper controls such as exact write approval metadata

Allowed variables:

- `ACCESS_TOKEN`: required system-user access token.
- `AD_ACCOUNT_ID`: required for most ad account commands.
- `BUSINESS_ID`: optional, used by some business, catalog, or dataset commands.

Rules:

- `.meta-profiles/*.env` and any root `.env` must be ignored by version control.
- A root `.env` must not be read by the wrapper.
- Profile files must be regular files owned by the current user with mode `0600`; symlinks are prohibited.
- Only the selected profile file may be opened. Inherited `ACCESS_TOKEN`, `AD_ACCOUNT_ID`, and `BUSINESS_ID` values must be removed before starting the official CLI.
- Command-line `--ad-account-id` and `--business-id` overrides are prohibited; IDs must come from the selected profile.
- Agents may check whether required variables exist, but must never print their values.
- Use redacted diagnostics such as `ACCESS_TOKEN=present`, `AD_ACCOUNT_ID=present`, or `BUSINESS_ID=missing`.
- Do not store credentials in user-level config directories.
- Do not put credentials in MCP server definitions, wrapper arguments, generated reports, or command transcripts.

## Required Wrapper Behavior

All Meta Ads CLI calls must pass through one shared local wrapper that controls:

- mandatory explicit profile selection
- single-profile credential loading
- allowed command families
- dry-run mode
- concurrency
- retries
- per-account pacing
- logging
- write approvals
- circuit breakers
- output redaction

Logs, locks, circuit breakers, and approvals must be isolated by profile. A
write approval digest must include the selected profile and the exact Meta Ads
CLI command so approval for one profile cannot be replayed against another.

Multiple agents, MCPs, or scripts must not run independent retry or pacing logic against the same ad account or token.

## Allowed Commands Model

Read commands may run by default when they use the approved wrapper and redact secrets:

- `meta auth status`
- `meta ads adaccount current`
- `meta ads adaccount list`
- `meta ads campaign list`
- `meta ads adset list`
- `meta ads ad list`
- `meta ads adcreative list`
- `meta ads page list`
- `meta ads dataset list`
- `meta ads catalog list`
- `meta ads insights get`

Write-capable commands require exact human approval before execution, including any command that can:

- create, update, delete, remove, pause, activate, publish, duplicate, upload, connect, or assign
- edit budgets or bids
- edit targeting
- edit creatives, copy, URLs, placements, tracking, pixels, datasets, catalogs, UTMs, or conversion settings
- change regulated-category or policy-sensitive settings

Approval must be for the exact command or exact structured diff. Broad approval like "optimize the account" is not enough for direct writes.

## Write Approval Requirements

Before executing a write, the agent must show:

- ad account ID
- object type and object ID when known
- old value when available
- new value
- reason for the change
- exact Meta Ads CLI command or structured operation
- expected impact
- rollback command or rollback plan
- batch size
- whether the operation is idempotent

If any of this information is unavailable, the agent must say so explicitly before asking for approval.

## Pacing and Batches

1. Use conservative pacing.
   - Writes must be serialized per ad account.
   - Reads may be parallelized modestly, but must still go through the shared wrapper.
   - Avoid large bursts of calls, especially on new apps, new ad accounts, new system users, or recently changed tokens.

2. Prefer cached data.
   - Sync reporting data into a local database, file cache, or warehouse when practical.
   - Let agents reason over local snapshots instead of repeatedly asking Meta for the same metrics.
   - Avoid repeated "probe" commands while planning.

3. Keep batches small.
   - Do not mass-edit many campaigns, ad sets, ads, or creatives in one run.
   - Start with one small batch, verify results, then continue only after review.

4. Stage risky changes.
   - First run in dry-run mode when the CLI supports it.
   - Then apply to a small scope.
   - Then verify through read-only commands.
   - Then expand gradually only if approved.

5. Avoid autonomous budget movement.
   - No unattended budget day-trading.
   - No recurring loop that changes bids or budgets every few minutes.
   - No automated "optimization" writes without a human-reviewed rulebook and hard limits.

## Retry Policy

1. Retry only safe failures.
   - Retry transient local command failures.
   - Retry clear temporary Meta or CLI service failures.
   - Do not classify rate-limit or request-volume signals as safe failures to retry; follow the immediate-stop rule below.

2. Never retry bad requests in a loop.
   - Do not retry auth errors.
   - Do not retry permission errors.
   - Do not retry validation errors.
   - Do not retry policy errors.
   - Do not retry malformed commands.
   - Do not retry missing asset or missing object errors.

3. Use strict limits.
   - Maximum 3 attempts per operation.
   - Use exponential backoff with jitter.
   - After repeated failures, stop and ask for human review.

4. Be extra careful with mutating commands.
   - Do not put outer retry loops around create/update/pause/activate/publish/upload commands.
   - Retry a write only when the operation is idempotent or when Meta Ads CLI confirms the change did not apply.
   - If write state is ambiguous, stop and inspect before taking another action.

## Immediate Stop on Request-Volume Signals

Any CLI or API response, warning, header, stderr message, or indirect hint that
Meta is receiving too many requests is an immediate stop condition. This rule
applies on the first signal; repeated errors are not required.

Signals include, but are not limited to:

- rate-limit, throttling, quota, or cooldown messages
- `too many requests` or `too many calls`
- `Please reduce the amount of data you're asking for`
- warnings about request volume, usage spikes, or excessive pagination
- ambiguous responses that reasonably suggest Meta is limiting request volume

When any such signal appears, the agent must:

1. Stop the entire current Meta workflow or batch immediately.
2. Make no further Meta CLI or API calls, including retries, smaller probes, pagination, read-only diagnostics, or calls through another profile or account.
3. Do not clear or bypass a circuit breaker and do not wait and resume autonomously.
4. Notify the user promptly, including the redacted signal, the command or phase that triggered it, what completed, what remains uncertain, and confirmation that no further calls were made.
5. Resume only after the user gives explicit direction and any applicable cooldown or safety condition has been reviewed.

## CLI-Native Monitoring

Watch CLI behavior and stop early when it becomes noisy or ambiguous:

- non-zero exit codes
- repeated stderr warnings
- repeated empty or malformed output
- repeated auth, permission, validation, or policy errors
- any rate-limit or request-volume signal, even if it appears only once
- sudden spike in failed commands
- unexpected object state after a write
- ambiguous result from a mutating command
- Meta account security warning
- ad account restriction warning
- app review or access warning

After a circuit breaker triggers for a reason unrelated to request volume, the agent may perform only read-only diagnostic commands unless a human explicitly approves recovery actions. If the breaker or its triggering response indicates excessive request volume, the immediate-stop rule applies and no further Meta calls are allowed before notifying the user.

## Creative and Policy-Sensitive Content

AI-generated ad content requires human review before upload or publish:

- copy
- images
- videos
- claims
- landing pages
- targeting changes
- regulated-category settings

The agent must not try to bypass policy enforcement:

- no evasion language
- no account cycling
- no proxy rotation
- no fake identities
- no CAPTCHA bypass
- no anti-detect tooling
- no "warm-up" schemes for throwaway accounts

## Logging Requirements

Log every Meta Ads CLI operation with:

- timestamp
- actor/tool/agent name
- selected profile
- ad account ID
- command family
- object ID when available
- read vs write classification
- redacted command summary
- exit code
- retry count
- summarized stderr/stdout outcome
- approval reference for writes
- rollback reference for writes

Logs must redact tokens, cookies, authorization strings, payment data, and personal contact data.

## Prohibited Agent Behavior

Agents must not:

- use browser automation to operate Ads Manager or mutate Meta settings
- scrape Meta pages
- call unofficial/private Meta surfaces
- run custom direct-call scripts for Meta Ads work
- rotate proxies or devices
- hide automation
- use personal user tokens when a system-user token is required
- run competing MCPs/scripts against the same account without a shared wrapper
- blindly discover commands, permissions, or assets by trial and error
- keep retrying after auth, permission, validation, policy, or rate-limit errors
- publish creative or targeting changes without review
- perform open-ended "optimize everything" write actions

## Safe Operating Modes

Preferred modes, from safest to riskiest:

1. Local analysis only.
   - Query cached data.
   - No Meta Ads CLI calls.

2. Read-only browser setup inspection.
   - Explicitly requested review of visible Meta App Settings or Business Settings only.
   - No data entry, form submission, secret handling, or state changes.

3. Read-only live reporting.
   - Official Meta Ads CLI.
   - System-user `ACCESS_TOKEN` from the explicitly selected private profile only.
   - Shared wrapper.

4. Dry-run write planning.
   - Draft exact changes.
   - No mutation.

5. Human-approved small write batch.
   - Exact diff approved.
   - Serialized per account.
   - Verified after execution.

6. Scheduled production automation.
   - Allowed only with explicit human-approved rules, hard limits, monitoring, circuit breakers, and rollback plans.

Mode 6 must never be introduced implicitly by an agent.
