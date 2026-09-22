# CLI compatibility

Reviewed on 2026-09-22 for policy v0.1.0.

## Reference package

| Item | Reference |
| --- | --- |
| Distribution | `meta-ads` |
| Version reviewed | `1.1.0` |
| Executable | `meta` |
| Python requirement | `>=3.12` in publisher metadata |
| Package status | Alpha in publisher metadata |
| Package license | `LicenseRef-Proprietary`; separate from this repository's MIT license |
| Publisher listing | [PyPI version page](https://pypi.org/project/meta-ads/1.1.0/) |
| Official documentation entry point | [Meta Ads CLI overview](https://developers.facebook.com/documentation/ads-commerce/ads-ai-connectors/ads-cli/ads-cli-overview) |

The PyPI project is maintained by the `facebook` account and identifies itself as the official CLI. Its version and metadata agree with the locally installed distribution. No upstream CLI binary or source is redistributed here.

## Verification method

The review read the installed distribution's `METADATA` and console entry point, then inspected embedded command descriptions and symbols in its compiled modules without importing or executing the CLI. The local package was already installed; this review did not establish its complete supply-chain provenance.

The official documentation URL returned HTTP 429 during this review. No additional Meta requests were made after that response. The URL is retained as the official documentation entry point, but its contents and current availability were not verified.

No credentials were loaded, no CLI commands were executed, and no advertising API requests were made. This is a static compatibility review, not an execution or integration test.

## Read-command checks

These are command names for wrapper implementers, not instructions to bypass the wrapper. Every live operation still requires the explicitly selected profile and approved wrapper described in the policy.

| Policy command | Static evidence in 1.1.0 |
| --- | --- |
| `meta auth status` | Embedded auth command example and `status` symbol |
| `meta ads adaccount current` | Account module description and `current` symbol |
| `meta ads adaccount list` | Publisher command table and embedded account description |
| `meta ads campaign list` | Publisher example and embedded campaign description |
| `meta ads adset list` | Publisher command table and embedded ad set description |
| `meta ads ad list` | Publisher command table and embedded ad description |
| `meta ads creative list` | Publisher command table and embedded creative description |
| `meta ads page list` | Publisher command table and embedded page description |
| `meta ads dataset list` | Publisher command table and embedded dataset description |
| `meta ads catalog list` | Publisher command table and embedded catalog description |
| `meta ads insights get` | Publisher example and embedded insights `get` symbol |

The initial template used `adcreative list`; v0.1.0 uses `creative list` to match the reviewed package. Static evidence does not verify runtime command registration, permissions, output schemas, or network behavior. Confirm local help in a credential-free environment before enabling a wrapper for your installed version.

## Limits that affect adoption

- **Dry-run remains unverified.** Do not assume a global or command-specific `--dry-run` exists. A wrapper planning mode must avoid dispatching writes; it cannot rely on an unverified CLI flag.
- **Configuration has fallback paths.** Embedded configuration documentation describes environment, project `.env`, and user-level configuration sources. Isolate all of them; merely clearing inherited environment values does not establish profile isolation.
- **Nested request behavior remains unverified.** This review does not prove how the CLI or its SDK retries or paginates. A wrapper must enforce the immediate-stop rule across those layers before live execution is approved.
- **Auth output needs review.** Treat authentication status output as potentially sensitive until its redaction is verified; do not forward raw diagnostics to an agent.
- **Platform support is constrained.** The publisher lists compiled wheels for specific Python/platform combinations. Check available release files for your interpreter and operating system before installation.
- **Upgrades require a new review.** Version 1.1.0 is the reviewed reference, not a promise that later versions are compatible or that this is the latest available version when you read this document.

See the [README wrapper checklist](../README.md#4-review-the-wrapper-checklist) and the [full policy](../META_ADS_AGENT_RULES.md) before adoption.
