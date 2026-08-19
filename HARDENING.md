<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/v4.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/v4.2.0** was hardened automatically. 15 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references actions using mutable version tags instead of pinned SHA digests: `actions/checkout@v4` (appears twice) and `infracost/actions/setup@v3`. These can be silently updated to malicious versions.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:49`

### unpinned-uses (severity: high)

codeql-analysis.yml references actions using mutable version tags: `actions/checkout@v4`, `github/codeql-action/init@v3`, `github/codeql-action/analyze@v3`.

Locations:

- `.github/workflows/codeql-analysis.yml:20`
- `.github/workflows/codeql-analysis.yml:23`
- `.github/workflows/codeql-analysis.yml:28`

### unpinned-uses (severity: high)

examples_test.yml references `actions/checkout@v4` (three times) using a mutable version tag instead of a pinned SHA digest.

Locations:

- `.github/workflows/examples_test.yml:16`
- `.github/workflows/examples_test.yml:22`
- `.github/workflows/examples_test.yml:28`

### unpinned-uses (severity: high)

scanner_release.yml references `actions/checkout@v4` and `actions/setup-go@v6` using mutable version tags instead of pinned SHA digests.

Locations:

- `.github/workflows/scanner_release.yml:107`
- `.github/workflows/scanner_release.yml:109`

### unpinned-uses (severity: high)

scanner_test.yml references multiple actions using mutable version tags: `actions/checkout@v4` (×4), `actions/setup-go@v6` (×4), `actions/cache@v4`, and `golangci/golangci-lint-action@v9`.

Locations:

- `.github/workflows/scanner_test.yml:21`
- `.github/workflows/scanner_test.yml:23`
- `.github/workflows/scanner_test.yml:32`
- `.github/workflows/scanner_test.yml:34`
- `.github/workflows/scanner_test.yml:38`
- `.github/workflows/scanner_test.yml:50`
- `.github/workflows/scanner_test.yml:52`
- `.github/workflows/scanner_test.yml:57`
- `.github/workflows/scanner_test.yml:63`
- `.github/workflows/scanner_test.yml:68`
- `.github/workflows/scanner_test.yml:70`

### unpinned-uses (severity: high)

setup_test.yml references `actions/checkout@v4` using a mutable version tag instead of a pinned SHA digest.

Locations:

- `.github/workflows/setup_test.yml:23`

### unpinned-uses (severity: high)

verify_dist.yml references `actions/checkout@v4` using a mutable version tag instead of a pinned SHA digest.

Locations:

- `.github/workflows/verify_dist.yml:21`

### missing-permissions (severity: medium)

scanner_test.yml has no top-level `permissions:` key and none of its jobs (test, integration, lint, mocks) define job-level permissions. This means the workflow runs with the default, overly broad token permissions.

Locations:

- `.github/workflows/scanner_test.yml:1`

### missing-permissions (severity: medium)

setup_test.yml has no top-level `permissions:` key and its only job (`test`) defines no job-level permissions. The workflow runs with default, overly broad token permissions.

Locations:

- `.github/workflows/setup_test.yml:1`

### missing-permissions (severity: medium)

verify_dist.yml has no top-level `permissions:` key and its only job (`verify-dist`) defines no job-level permissions. The workflow runs with default, overly broad token permissions.

Locations:

- `.github/workflows/verify_dist.yml:1`

### github-env-injection (severity: high)

diff/action.yml — 'Determine version' step: the `write_output` function writes `$VERSION` (sourced from `inputs.version` via the `VERSION` env var) to `$GITHUB_OUTPUT` using a heredoc-delimiter approach but without the required `printf '%s' ... | tr -d '\n\r'` sanitization. An attacker-controlled version string containing newlines could inject additional output variables.

Locations:

- `diff/action.yml:71`

### github-env-injection (severity: high)

diff/action.yml — 'Derive context' step: the `write_output` function writes multiple values derived from untrusted inputs (`inputs.github-owner`, `inputs.github-repo`, `inputs.pr-number`, `inputs.repo-url`, `inputs.pr-status`, `github.event.pull_request.title`, `github.event.pull_request.user.login`, `github.event.pull_request.labels.*.name`) to `$GITHUB_OUTPUT` without the required `printf '%s' ... | tr -d '\n\r'` sanitization step.

Locations:

- `diff/action.yml:130`

### github-env-injection (severity: high)

scan/action.yml — 'Determine version' step: the `write_output` function writes `$VERSION` (sourced from `inputs.version` via the `VERSION` env var) to `$GITHUB_OUTPUT` without the required `printf '%s' ... | tr -d '\n\r'` sanitization step.

Locations:

- `scan/action.yml:57`

### github-env-injection (severity: high)

scan/action.yml — 'Derive context' step: the `write_output` function writes `$REPO_URL` (sourced from `inputs.repo-url` via `INPUT_REPO_URL`) to `$GITHUB_OUTPUT` without the required `printf '%s' ... | tr -d '\n\r'` sanitization step.

Locations:

- `scan/action.yml:90`

### github-env-injection (severity: high)

scanner_release.yml — 'version' job step: the `write_output` function writes `${RAW_VERSION#v}` to `$GITHUB_OUTPUT`, where `$RAW_VERSION` is set from `$INPUT_VERSION` (sourced from `github.event.inputs.version`, a user-supplied `workflow_dispatch` input) without the required `printf '%s' ... | tr -d '\n\r'` sanitization step. A malicious version string with embedded newlines could inject additional output variables.

Locations:

- `.github/workflows/scanner_release.yml:48`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all findings across 7 files:

1. action.yml: Pinned actions/checkout@v4 (×2) and infracost/actions/setup@v3 to full SHA digests.

2. .github/workflows/codeql-analysis.yml: Pinned actions/checkout@v4, github/codeql-action/init@v3, and github/codeql-action/analyze@v3 to SHA digests.

3. .github/workflows/examples_test.yml: Pinned all three actions/checkout@v4 references.

4. .github/workflows/scanner_release.yml: Pinned actions/checkout@v4 and actions/setup-go@v6; fixed github-env-injection by adding safe_value=$(printf '%s' "$value" | tr -d '\n\r') sanitization in write_output.

5. .github/workflows/scanner_test.yml: Added permissions: {} top-level and contents: read per-job; pinned actions/checkout@v4 (×4), actions/setup-go@v6 (×4), actions/cache@v4, and golangci/golangci-lint-action@v9.

6. .github/workflows/setup_test.yml: Added permissions: {} top-level and contents: read job-level; pinned actions/checkout@v4.

7. .github/workflows/verify_dist.yml: Added permissions: {} top-level and contents: read job-level; pinned actions/checkout@v4.

8. diff/action.yml: Fixed github-env-injection in both write_output functions (Determine version and Derive context steps) by sanitizing values with tr -d '\n\r'.

9. scan/action.yml: Fixed github-env-injection in both write_output functions (Determine version and Derive context steps) by sanitizing values with tr -d '\n\r'.

SHAs used: actions/checkout → 11bd71901bbe5b1630ceea73d27597364c9af683 (v4.2.2), infracost/actions/setup → d5dd739fb5eae81f31bf09fea5c290186a91964a (v3.0.0), github/codeql-action → 60168efe1c415ce0f5521ea06d5c2062adbeed1b (v3.28.17), actions/setup-go → 924ae3a1cded613372ab5595356fb5720e22ba16 (v6), actions/cache → 5a3ec84eff668545956fd18022155c47e93e2684 (v4.2.3), golangci/golangci-lint-action → ba0d7d2ec06a0ea1cb5fa41b2e4a3ab91d21278a (v9).

