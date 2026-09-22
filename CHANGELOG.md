# Changelog

## 0.1.0 — 2026-09-22

Initial release of the Meta Ads agent policy template.

### Included

- A reusable Meta Ads agent policy covering explicit profiles, controlled execution, exact write approvals, audit logging, and immediate stops on request-volume signals.
- An adoption guide with a non-overwriting installation example and a wrapper acceptance checklist.
- An explicit agent workflow to create a missing wrapper locally, test it offline, and present it for approval before live use.
- A static compatibility review for `meta-ads` 1.1.0, with command evidence and explicit verification limits.
- Short contribution and issue-reporting guidelines, plus the MIT license for this repository's original template and documentation.

### Corrected and clarified

- Use `meta ads creative list` in place of `meta ads adcreative list` for the reviewed CLI package.
- Require isolation from project, ancestor, and user-level configuration fallbacks in the child CLI process.
- Require control of nested retries and pagination, and rejection of unreviewed commands and flags.
- Explain that agent instructions alone cannot enforce access restrictions.

### Known limitations

- No wrapper, credentials, CLI distribution, or runtime enforcement is included.
- CLI review is static; live API behavior, runtime command registration, native dry-run, retries, and pagination have not been validated.
- The official Meta documentation endpoint returned HTTP 429; further Meta requests stopped immediately.

See [the release review](docs/RELEASE_REVIEW.md) for audit scope and verification limits.
