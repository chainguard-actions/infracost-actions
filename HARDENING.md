<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.4** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three actions using mutable tag refs instead of pinned 40-character SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved: `actions/checkout@v4` (used twice) and `infracost/actions/setup@v3`.

Locations:

- `action.yml:24`
- `action.yml:30`
- `action.yml:44`

### github-env-injection (severity: high)

diff/action.yml — 'Determine version' step: the `write_output` function writes `VERSION` (sourced from `inputs.version` via the `VERSION` env var) to `$GITHUB_OUTPUT` using `printf '%s\n' "$value"` without stripping newlines (`tr -d '\n\r'`). An attacker-controlled version string containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `diff/action.yml:47`

### github-env-injection (severity: high)

diff/action.yml — 'Derive context' step: the `write_output` function writes multiple untrusted values to `$GITHUB_OUTPUT` without newline sanitization (`tr -d '\n\r'`). Affected values include: `PR_TITLE` (from `github.event.pull_request.title`), `PR_AUTHOR` (from `github.event.pull_request.user.login`), `PR_LABELS` (from `github.event.pull_request.labels.*.name`), `OWNER` (from `inputs.github-owner`), `REPO` (from `inputs.github-repo`), `PR` (from `inputs.pr-number`), and `REPO_URL` (from `inputs.repo-url`). A value containing embedded newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `diff/action.yml:131`

### github-env-injection (severity: high)

scan/action.yml — 'Determine version' step: the `write_output` function writes `VERSION` (sourced from `inputs.version` via the `VERSION` env var) to `$GITHUB_OUTPUT` using `printf '%s\n' "$value"` without stripping newlines (`tr -d '\n\r'`). An attacker-controlled version string containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `scan/action.yml:47`

### github-env-injection (severity: high)

scan/action.yml — 'Derive context' step: the `write_output` function writes `REPO_URL` (sourced from `inputs.repo-url` via the `INPUT_REPO_URL` env var) to `$GITHUB_OUTPUT` using `printf '%s\n' "$value"` without stripping newlines (`tr -d '\n\r'`). A caller-supplied repo-url containing embedded newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `scan/action.yml:96`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed all 5 findings:
1. Pinned actions/checkout@v4 (used twice) to @11d5960a326750d5838078e36cf38b85af677262 # v4 in action.yml.
2. Pinned infracost/actions/setup@v3 to @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3 in action.yml.
3. Updated write_output() in diff/action.yml 'Determine version' step to sanitize values with `value=$(printf '%s' "$2" | tr -d '\n\r')` before writing to GITHUB_OUTPUT.
4. Updated write_output() in diff/action.yml 'Derive context' step with the same newline sanitization.
5. Updated write_output() in scan/action.yml 'Determine version' step with the same newline sanitization.
6. Updated write_output() in scan/action.yml 'Derive context' step with the same newline sanitization.

