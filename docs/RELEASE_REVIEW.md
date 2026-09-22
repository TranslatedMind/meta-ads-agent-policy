# v0.1.0 release review

Review date: 2026-09-22. Scope: this standalone policy repository and its v0.1.0 release, not the originating workspace or any advertising account.

## Repository audit

- The initial history contained one commit and four tracked files. The only remote Git reference was `main`; no remote releases, issues, or Actions runs existed at the start of the review.
- Gitleaks 8.30.1 scanned all reachable Git history using its default rules with full output redaction. No secret findings were reported. The scanner ran locally; repository contents were not sent to an external scanning service. Its downloaded release archive was checked against the publisher's SHA-256 digest and checksums file.
- A supplemental inspection checked historical file contents for email addresses, local home paths, real-looking Meta account IDs, private/internal hosts, credential-bearing URLs, and private-key headers. No such content was found in the initial history.
- Commit identities use the repository owner's GitHub noreply address. The owner handle, copyright attribution, and public package/documentation links are intentional publication metadata.
- The release files were also scanned with Gitleaks. Secret scanning is pattern based and cannot establish the absence of every possible confidential detail or confirm rights to publish.
- Only this repository's template and documentation are included. No source-workspace history, profiles, tokens, reports, wrappers, or installed CLI binaries are included.

## Document checks

- The MIT text matches GitHub's canonical template after substituting the year and copyright holder and normalizing whitespace. The upstream CLI has a separate proprietary license; this repository does not relicense it.
- Markdown files are in English. Relative links and heading anchors resolve within the release tree; code fences are balanced, and Git whitespace checks pass.
- The README adoption example was checked using temporary projects: it copies the policy when no `AGENTS.md` exists and refuses to overwrite an existing file.
- The full immediate-stop section was preserved. The template now explicitly directs agents to create and validate a missing wrapper locally, then present it for review before live use.
- PyPI, GitHub security documentation, and the MIT reference were accessible during the review. The Meta documentation link returned HTTP 429 and was not retried.

## Compatibility boundary

See [CLI_COMPATIBILITY.md](CLI_COMPATIBILITY.md). The package version and command spellings were reviewed statically. Native dry-run, runtime dispatch, configuration isolation, nested retries, pagination, and actual API behavior remain unverified. No live Meta operations were performed.

The policy can be shared as a template with these limits stated. It does not certify a production-ready wrapper.

## Maintenance notes

- Maintainers and contributors are responsible for having the rights to publish their contributions under MIT. This technical audit does not establish ownership obligations.
- Keep GitHub issues enabled for feedback. [SECURITY.md](../SECURITY.md) describes a simple reporting process using synthetic examples; no separate private reporting channel is required.
- If files, history, or release assets change after this review, review those changes and repeat the affected checks before the next release.
