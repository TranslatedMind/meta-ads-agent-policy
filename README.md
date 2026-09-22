# Meta Ads Agent Policy

A reusable policy template for AI agents working with Meta Ads through the official Meta Ads CLI and a controlled local wrapper.

The template defines how agents handle credentials, isolate accounts, request approval for changes, pace requests, and stop when Meta signals excessive request volume.

**Start with [META_ADS_AGENT_RULES.md](META_ADS_AGENT_RULES.md).**

> Community project. Not an official Meta document and not affiliated with or endorsed by Meta.
> This repository contains instructions, not an executable CLI, wrapper, or security enforcement layer.

## What the policy covers

- **Explicit profiles:** select one isolated app, token, and ad account for every operation; no default profile.
- **Credential handling:** keep system-user tokens in private local profile files and out of prompts, command arguments, logs, and version control.
- **Controlled execution:** route Meta Ads operations through a shared local wrapper with approval checks, redaction, pacing, and circuit breakers.
- **Human approval:** require an exact command or structured diff before a write, with expected impact and a rollback plan.
- **Request-volume stops:** stop the entire Meta workflow on the first throttling or excessive-request signal; no retries, pagination, alternate profiles, or autonomous resumption.
- **Limited browser inspection:** allow explicitly requested, read-only review of App Settings or Business Settings; prohibit browser operation of Ads Manager.
- **Small, verified changes:** serialize writes, keep batches small, and verify approved changes through read-only commands.

The template is the complete policy; this README is an introduction.

## Adopt it in a project

### 1. Add the instructions

Copy `META_ADS_AGENT_RULES.md` into your target project as `AGENTS.md` so your agent can load it as project instructions.

If the project already has an `AGENTS.md`, merge the relevant sections and resolve conflicts instead of overwriting existing instructions. For agents that use a different instruction mechanism, add the policy through that mechanism and verify that it is loaded.

Keep this repository's MIT license and copyright notice with copies or substantial portions of the template. If your project has a different license, preserve this notice separately, for example as `LICENSES/meta-ads-agent-policy-MIT.txt`.

### 2. Supply a local wrapper

The intended execution path is:

```text
agent → explicit isolated profile → approved local wrapper → official Meta Ads CLI → Meta
```

The template uses these example conventions:

| Path | Purpose |
| --- | --- |
| `scripts/meta` | Your locally implemented and reviewed CLI wrapper |
| `.meta-profiles/<profile>.env` | Private credentials and account configuration for one profile |

Neither the wrapper nor profile files are included. Implement and review the wrapper against the policy's **Required Wrapper Behavior** section before allowing live operations. It must enforce the requirements in code; instructions alone cannot provide those controls.

### 3. Configure private profiles

The template expects `ACCESS_TOKEN`, `AD_ACCOUNT_ID`, and optionally `BUSINESS_ID` in the explicitly selected profile file. Each file must be owned by the current user, have mode `0600`, and be a regular file, not a symlink.

Add these exclusions to the target project's `.gitignore` before creating credentials:

```gitignore
.env
.env.*
.meta-profiles/
```

The wrapper must load only the selected profile, clear inherited Meta credential and account variables, reject account overrides, and isolate logs, locks, approvals, and circuit breakers by profile. See the template for the full contract.

### 4. Verify compatibility before execution

Check the installed CLI's local documentation or help to confirm command names and dry-run support. The command list in the template is a policy model, not a compatibility guarantee for every CLI version.

Adapt example paths consistently if your project uses other conventions. If the required wrapper or safeguards are missing, keep work to local analysis and command drafting. Dry-run planning does not authorize a live write.

## Scope and limitations

This is a starting point for a project policy. It does not provision Meta access, grant permissions, implement rate limiting, run reports, or make changes to an account. Actual access depends on your Meta configuration, and enforcement depends on your wrapper and agent environment.

The rules deliberately require human involvement for write operations and recovery after request-volume signals. Review them against your own workflow before adoption.

## Contributing

Issues and pull requests are welcome when you have access to this repository. For a proposed policy change, explain the scenario, the current behavior, and the intended behavior. Keep examples generic and redact account identifiers, credentials, and client data.

## License

[MIT](LICENSE), copyright © 2026 TranslatedMind.

MIT makes the template easy to reuse and adapt in both open-source and commercial projects while requiring preservation of the copyright and license notice. The license covers this repository's template and documentation; it does not grant rights to Meta products or trademarks.

See [Choose a License's MIT reference](https://choosealicense.com/licenses/mit/) for the license text and a summary of its terms.
