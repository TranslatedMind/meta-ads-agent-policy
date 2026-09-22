# Contributing

This project accepts focused improvements to the Meta Ads agent policy and its documentation. It does not contain a CLI or wrapper implementation.

## Propose a change

Open an issue or pull request describing the scenario, the current rule, the proposed behavior, and any effect on approval, credential isolation, or request-volume handling. Use small, synthetic examples. For CLI compatibility changes, include the package version and a primary source or redacted local evidence.

Keep contributions and documentation in English. Prefer small diffs, preserve the immediate-stop rule, and update related documentation and the changelog when behavior changes. Discuss substantial relaxations of safeguards before implementing them.

## Before submitting

- Check Markdown formatting, relative links, and consistency with the policy.
- Do not include tokens, real account IDs, client data, private URLs, logs containing sensitive information, or credentials in screenshots.
- Keep new examples independent of live accounts; no live Meta operations are required to contribute.
- Run `git diff --check` and review the complete diff. When changing an adoption example, verify it locally with temporary files.
- For a suspected security issue, follow [SECURITY.md](SECURITY.md) instead of posting exploit details in an issue or pull request.

By submitting a contribution, you agree to license it under this repository's [MIT license](LICENSE) and confirm that you have the right to contribute it under those terms. No separate contributor license agreement is required.

Maintenance is best effort. There is no guaranteed response time, acceptance, or support for older revisions. Keep discussions respectful and focused on the proposed change.
