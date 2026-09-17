<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three action steps using mutable version tags instead of pinned SHA digests: `actions/checkout@v4` (used twice) and `infracost/actions/setup@v3`. Any of these tags could be moved to a different commit, enabling a supply-chain attack.

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:46`

### github-env-injection (severity: high)

diff/action.yml 'Determine version' step writes the env var `$VERSION` (sourced from `inputs.version` via `VERSION: ${{ inputs.version }}`) to `$GITHUB_OUTPUT` through the `write_output` helper function without applying the required `printf '%s' ... | tr -d '\n\r'` sanitization. A caller-controlled newline in the version string could inject additional key=value pairs into the output file.

Locations:

- `diff/action.yml:65`

### github-env-injection (severity: high)

diff/action.yml 'Derive context' step writes multiple untrusted values to `$GITHUB_OUTPUT` via the `write_output` helper without `tr -d '\n\r'` sanitization. Affected variables include: `$PR_TITLE` (from `github.event.pull_request.title`), `$PR_AUTHOR` (from `github.event.pull_request.user.login`), `$PR_LABELS` (from `github.event.pull_request.labels.*.name`), `$OWNER` (from `inputs.github-owner`), `$REPO` (from `inputs.github-repo`), and `$PR` (from `inputs.pr-number`). An attacker-controlled newline in any of these values could inject additional outputs.

Locations:

- `diff/action.yml:152`

### github-env-injection (severity: high)

scan/action.yml 'Determine version' step writes the env var `$VERSION` (sourced from `inputs.version` via `VERSION: ${{ inputs.version }}`) to `$GITHUB_OUTPUT` through the `write_output` helper function without applying the required `printf '%s' ... | tr -d '\n\r'` sanitization. A caller-controlled newline in the version string could inject additional key=value pairs into the output file.

Locations:

- `scan/action.yml:47`

### github-env-injection (severity: high)

scan/action.yml 'Derive context' step writes `$REPO_URL` (sourced from `inputs.repo-url` via `INPUT_REPO_URL: ${{ inputs.repo-url }}`) to `$GITHUB_OUTPUT` through the `write_output` helper function without applying the required `printf '%s' ... | tr -d '\n\r'` sanitization. A caller-controlled newline in the repo-url input could inject additional key=value pairs into the output file.

Locations:

- `scan/action.yml:89`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed 3 unpinned action references in action.yml by pinning to full commit SHAs (actions/checkout@v4 → SHA 11d5960a..., infracost/actions/setup@v3 → SHA e9d6e6cd...). Fixed 4 github-env-injection findings by modifying the write_output() helper function in each affected step to sanitize values using `printf '%s' "$2" | tr -d '\n\r'` before writing to GITHUB_OUTPUT. This prevents attacker-controlled newlines in inputs like version, PR title, PR author, PR labels, github-owner, github-repo, pr-number, and repo-url from injecting additional key=value pairs into the output file.

