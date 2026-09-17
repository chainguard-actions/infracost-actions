<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses three `uses:` references pinned to mutable tags instead of full 40-character SHA commit digests. These can be silently updated by the upstream repository, enabling supply-chain attacks. Failing references: `actions/checkout@v4` (line 25), `infracost/actions/setup@v3` (line 31), `actions/checkout@v4` (line 44).

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:44`

### github-env-injection (severity: high)

In diff/action.yml, the 'Determine version' step writes `$VERSION` (sourced from `inputs.version` via env var) to $GITHUB_OUTPUT using `printf '%s\n' "$value"` inside the write_output() helper without `tr -d '\n\r'` sanitization. A newline in the value could inject additional output entries. Similarly, the 'Derive context' step writes multiple untrusted values — including `$PR_TITLE` (from `github.event.pull_request.title`), `$PR_AUTHOR` (from `github.event.pull_request.user.login`), `$PR_LABELS` (from `github.event.pull_request.labels.*.name`), `$OWNER` (from `inputs.github-owner`), `$REPO` (from `inputs.github-repo`), `$PR` (from `inputs.pr-number`), and `$REPO_URL` (from `inputs.repo-url`) — all to $GITHUB_OUTPUT without newline sanitization. The random heredoc delimiter prevents delimiter collision but does not strip embedded newlines from the values themselves.

Locations:

- `diff/action.yml:41`
- `diff/action.yml:100`

### github-env-injection (severity: high)

In scan/action.yml, the 'Determine version' step writes `$VERSION` (sourced from `inputs.version` via env var) to $GITHUB_OUTPUT using `printf '%s\n' "$value"` inside the write_output() helper without `tr -d '\n\r'` sanitization. The 'Derive context' step similarly writes `$REPO_URL` (sourced from `inputs.repo-url`) to $GITHUB_OUTPUT without newline sanitization. An attacker-controlled newline in these values could inject additional output entries.

Locations:

- `scan/action.yml:24`
- `scan/action.yml:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed all three findings:

1. unpinned-uses (action.yml): Pinned all three `uses:` references to full 40-character SHA digests:
   - actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 # v4 (two occurrences, lines 25 and 44)
   - infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3 (line 31)

2. github-env-injection (diff/action.yml): Added newline sanitization to both write_output() helper functions (in 'Determine version' and 'Derive context' steps). Each now computes `safe_value=$(printf '%s' "$value" | tr -d '\n\r')` and writes `$safe_value` to GITHUB_OUTPUT instead of the raw `$value`.

3. github-env-injection (scan/action.yml): Same fix applied to both write_output() helper functions in scan/action.yml ('Determine version' and 'Derive context' steps).

