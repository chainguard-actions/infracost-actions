<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml uses three `uses:` references pinned to mutable version tags instead of immutable full SHA commits. This exposes the action to supply-chain attacks if the referenced tags are moved or the upstream repositories are compromised.

Failing references:
- `uses: actions/checkout@v4` (line 25)
- `uses: infracost/actions/setup@v3` (line 32)
- `uses: actions/checkout@v4` (line 46)

Each should be replaced with a full 40-character hex commit SHA, e.g. `uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:46`

### github-env-injection (severity: high)

In diff/action.yml, the `write_output()` helper function writes values derived from untrusted inputs directly to `$GITHUB_OUTPUT` without the required `printf '%s' ... | tr -d '\n\r'` sanitization. The values written include attacker-controllable data sourced from `github.event.pull_request.title` (→ `$PR_TITLE`), `github.event.pull_request.user.login` (→ `$PR_AUTHOR`), `github.event.pull_request.labels.*.name` (→ `$PR_LABELS`), and various `inputs.*` values (github-owner, github-repo, pr-number, repo-url, pr-status, base-path, head-path). Although a randomised heredoc delimiter is used, the values themselves are not stripped of newline characters before being written, violating the required sanitisation step.

Locations:

- `diff/action.yml:168`
- `diff/action.yml:169`
- `diff/action.yml:170`
- `diff/action.yml:171`
- `diff/action.yml:172`
- `diff/action.yml:173`
- `diff/action.yml:174`

### github-env-injection (severity: high)

In scan/action.yml, the `write_output()` helper function writes `$REPO_URL` (derived from `inputs.repo-url`, an attacker-controllable input) to `$GITHUB_OUTPUT` without the required `printf '%s' ... | tr -d '\n\r'` sanitization. Although a randomised heredoc delimiter is used, the value is not stripped of newline characters before being written, violating the required sanitisation step.

Locations:

- `scan/action.yml:91`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed three unpinned `uses:` references in action.yml by pinning to full commit SHAs: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 (used twice) and infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832. Fixed github-env-injection in diff/action.yml and scan/action.yml by updating the write_output() helper function in each file's 'Derive context' step to sanitize values with `safe_value=$(printf '%s' "$value" | tr -d '\n\r')` before writing to $GITHUB_OUTPUT, preventing newline injection attacks from attacker-controlled inputs like PR title, author, labels, and repo-url.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in both diff/action.yml and scan/action.yml. In the 'Determine version' step of each file, the write_output() function was missing newline sanitization before writing to $GITHUB_OUTPUT. Added `local safe_value` and `safe_value=$(printf '%s' "$value" | tr -d '\n\r')`, then changed `printf '%s\n' "$value"` to `printf '%s\n' "$safe_value"`. This matches the sanitization pattern already correctly used in the 'Derive context' step of both files, preventing an attacker from injecting newlines via the inputs.version parameter to poison $GITHUB_OUTPUT.

