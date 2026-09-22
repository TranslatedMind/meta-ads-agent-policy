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

## CLI reference and release status

Policy version: **0.1.0**. Reference package: **`meta-ads` 1.1.0**, reviewed on **2026-09-22** using publisher metadata and static inspection of locally installed package files.

- [Meta Ads CLI overview](https://developers.facebook.com/documentation/ads-commerce/ads-ai-connectors/ads-cli/ads-cli-overview)
- [Publisher package on PyPI](https://pypi.org/project/meta-ads/1.1.0/)
- [Command checks, evidence, and limitations](docs/CLI_COMPATIBILITY.md)

The executable is `meta`; similarly named packages are not interchangeable. The CLI is separately licensed and is not included in this MIT-licensed template repository. This release does not certify a working wrapper or live API compatibility.

## Adopt it in a project

### 1. Add the instructions

Download or clone this repository at the version you intend to adopt, then copy `META_ADS_AGENT_RULES.md` into your target project as `AGENTS.md`. Record the policy version in your project so later updates can be reviewed as a diff. For this release, use the `v0.1.0` tag.

The following local example intentionally refuses to replace an existing instruction file. Replace the example project path first:

```sh
# Run from this repository's root.
python3 - <<'PYTHON'
from pathlib import Path

project = Path("/path/to/your/project")
with (project / "AGENTS.md").open("x") as target:
    target.write(Path("META_ADS_AGENT_RULES.md").read_text())
PYTHON
```

There is no installer for this policy. Install the CLI separately from its publisher after checking Python and platform requirements in the compatibility notes.

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

Neither the wrapper nor profile files are included. The policy explicitly directs an agent to create a missing wrapper locally, validate it with synthetic fixtures, and present it for review. See **Bootstrap a Missing Wrapper** in the template. An existing wrapper should be inspected and improved with focused changes.

An example request to your agent:

> Set up the local wrapper required by AGENTS.md. Implement missing safeguards, test with a fake CLI and synthetic profiles, and show the results for review. Do not load real credentials or call Meta.

The wrapper must enforce the requirements in code; instructions alone cannot provide those controls. Local tests are necessary but do not establish how the real CLI handles configuration, retries, or pagination.

### 3. Configure private profiles

The template expects `ACCESS_TOKEN`, `AD_ACCOUNT_ID`, and optionally `BUSINESS_ID` in the explicitly selected profile file. Each file must be owned by the current user, have mode `0600`, and be a regular file, not a symlink.

Add these exclusions to the target project's `.gitignore` before creating credentials:

```gitignore
.env
.env.*
.meta-profiles/
```

The wrapper must load only the selected profile, clear inherited Meta credential and account variables, reject account overrides, and isolate logs, locks, approvals, and circuit breakers by profile. See the template for the full contract.

### 4. Review the wrapper checklist

Use local fixtures and mocked CLI responses to verify these controls before connecting a real account. This is an acceptance checklist for your implementation, not a list of implemented features in this repository.

- [ ] Require exactly one explicit profile; reject missing, unknown, or multiple profiles.
- [ ] Check profile ownership, mode `0600`, and regular-file status; reject symlinks and load only the selected profile.
- [ ] Remove inherited credential/account values, load the selected values, and isolate the child CLI from project or ancestor `.env` files and user-level configuration. Fail closed on missing configuration.
- [ ] Allow only reviewed commands and flags; reject account/business overrides and unknown operations. Classify writes independently of how an agent describes them.
- [ ] Bind write approval to the exact profile and operation, including referenced payload contents; reject altered commands, changed payloads, and cross-profile reuse.
- [ ] Share pacing across workers and serialize writes per ad account, even when multiple profiles reference that account. Keep profile audit state isolated.
- [ ] Bound retries across wrapper, CLI, and SDK layers. Stop automatic pagination and retries at the first request-volume signal, persist the stop, and require human-directed recovery.
- [ ] Produce redacted audit logs and safe agent-visible output, including auth diagnostics, errors, and debug paths.
- [ ] Make planning mode incapable of sending a write. Use native dry-run only after verifying that the selected command supports it and its behavior meets the policy.
- [ ] Verify approved writes with reads and retain a rollback plan; stop on ambiguous outcomes. The request-volume stop takes precedence over verification reads.

If any required control is missing or cannot be verified, keep live execution disabled and continue local implementation, offline validation, analysis, and command drafting.

### 5. Verify compatibility before execution

Check the installed CLI's local documentation or help to confirm command names and dry-run support. The command list in the template is a policy model, not a compatibility guarantee for every CLI version.

Adapt example paths consistently if your project uses other conventions. If the required wrapper or safeguards are missing, follow the bootstrap workflow and keep all work local until the controls are verified and the wrapper is approved. Dry-run planning does not authorize a live write.

## Scope and limitations

This is a starting point for a project policy. It does not provision Meta access, grant permissions, implement rate limiting, run reports, or make changes to an account. Actual access depends on your Meta configuration, and enforcement depends on your wrapper and agent environment. An agent with unrestricted shell access and access to credentials can bypass a wrapper; restrict execution paths and credential access outside the model if enforcement is required. Prompt instructions are not a security boundary.

The rules deliberately require human involvement for write operations and recovery after request-volume signals. Review them against your own workflow before adoption.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for focused changes and review expectations, and [SECURITY.md](SECURITY.md) for reporting problems through GitHub issues using examples without sensitive data. Maintenance is best effort, with no guaranteed response time.

## Release notes

See [CHANGELOG.md](CHANGELOG.md) for `v0.1.0` and [the release review](docs/RELEASE_REVIEW.md) for verification scope and remaining limitations.

## License

[MIT](LICENSE), copyright © 2026 TranslatedMind.

MIT makes the template easy to reuse and adapt in both open-source and commercial projects while requiring preservation of the copyright and license notice. The license covers this repository's template and documentation; it does not grant rights to Meta products or trademarks.

See [Choose a License's MIT reference](https://choosealicense.com/licenses/mit/) for the license text and a summary of its terms.
