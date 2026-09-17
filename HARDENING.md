<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml uses three unpinned action references with mutable tags instead of full 40-character commit SHAs: `actions/checkout@v4` (used twice) and `infracost/actions/setup@v3`. These can be silently updated by the upstream repository, enabling supply-chain attacks.

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:44`

### github-env-injection (severity: high)

In diff/action.yml, the `Determine version` step writes `$VERSION` (sourced from `inputs.version`) and `scanner/v${VERSION}` to `$GITHUB_OUTPUT` via the `write_output` helper using `printf '%s\n' "$value"`, which does NOT strip newlines. The `Derive context` step similarly writes multiple untrusted values — `$OWNER` (from `inputs.github-owner`), `$REPO` (from `inputs.github-repo`), `$PR` (from `inputs.pr-number`), `$REPO_URL` (from `inputs.repo-url`), `$PR_TITLE` (from `github.event.pull_request.title`), `$PR_AUTHOR` (from `github.event.pull_request.user.login`), `$PR_LABELS` (from `github.event.pull_request.labels.*.name`), and `$PR_STATUS` (from `inputs.pr-status`) — all to `$GITHUB_OUTPUT` without the required `printf '%s' ... | tr -d '\n\r'` sanitization step. An attacker-controlled newline in any of these values could corrupt the output file.

Locations:

- `diff/action.yml:66`
- `diff/action.yml:67`
- `diff/action.yml:155`
- `diff/action.yml:156`
- `diff/action.yml:157`
- `diff/action.yml:158`
- `diff/action.yml:159`
- `diff/action.yml:160`
- `diff/action.yml:161`
- `diff/action.yml:163`

### github-env-injection (severity: high)

In scan/action.yml, the `Determine version` step writes `$VERSION` (sourced from `inputs.version`) and `scanner/v${VERSION}` to `$GITHUB_OUTPUT` via the `write_output` helper using `printf '%s\n' "$value"`, which does NOT strip newlines. The `Derive context` step writes `$REPO_URL` (sourced from `inputs.repo-url`) to `$GITHUB_OUTPUT` without the required `printf '%s' ... | tr -d '\n\r'` sanitization step.

Locations:

- `scan/action.yml:66`
- `scan/action.yml:67`
- `scan/action.yml:100`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed 3 findings across 3 files:

1. action.yml (unpinned-uses): Pinned `actions/checkout@v4` (×2) to SHA `11d5960a326750d5838078e36cf38b85af677262` and `infracost/actions/setup@v3` to SHA `e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832`, preserving the original tags as inline comments.

2. diff/action.yml (github-env-injection): Updated the `write_output` helper in both the `Determine version` step and the `Derive context` step to sanitize values with `safe_value=$(printf '%s' "$value" | tr -d '\n\r')` before writing to `$GITHUB_OUTPUT`, preventing newline injection attacks from user-controlled inputs (version, github-owner, github-repo, pr-number, repo-url, pr-status, PR title, PR author, PR labels).

3. scan/action.yml (github-env-injection): Updated the `write_output` helper in both the `Determine version` step and the `Derive context` step with the same newline-stripping sanitization, protecting the version and repo-url outputs.

