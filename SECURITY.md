# Security policy

This repository contains policy text and documentation. It does not implement a security boundary or ship a runtime. Reports about instructions that could cause credential exposure, unintended writes, approval bypass, or failure to stop on request-volume signals are in scope.

## Reporting

Do not put tokens, client data, account identifiers, or exploit details in an issue, pull request, or discussion.

If this repository's **Security → Advisories** page offers **Report a vulnerability**, use that private reporting form. Its availability depends on repository visibility and settings; this document does not claim it is currently enabled.

If the form is unavailable and you do not already have an agreed private channel with the maintainer, open an issue titled **Private security contact requested**. Include only a request for a private reporting channel. Wait for that channel before sharing details; do not include a reproduction or sensitive attachments in the issue.

Once a private channel is established, include the policy version or commit, affected section, expected and observed behavior, impact, and a minimal synthetic reproduction. Testing against other people's accounts or credentials is not authorized by this policy. If a credential was exposed, its owner should revoke or rotate it rather than send it to the maintainer.

## Scope and support

Reports are reviewed on a best-effort basis against the latest policy revision. No response deadline, remediation deadline, bug bounty, or backport commitment is promised. Report defects in Meta's CLI or services through the vendor's own reporting process; this project cannot fix those products.

## Before public launch

The maintainer should enable and verify GitHub private vulnerability reporting when the repository becomes public, then confirm that reports reach a monitored notification channel. See [GitHub's configuration guide](https://docs.github.com/en/code-security/how-tos/report-and-fix-vulnerabilities/configure-vulnerability-reporting/configure-for-a-repository).
