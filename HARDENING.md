<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 1 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml uses mutable tag-based references instead of pinned full SHA commit hashes. This exposes the action to supply-chain attacks where a tag could be silently moved to point to malicious code. Failing references: `actions/checkout@v4` (line 25), `infracost/actions/setup@v3` (line 32), `actions/checkout@v4` (line 45). Each should be replaced with a full 40-character hex SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:45`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced all three mutable tag-based `uses:` references in hardened/action/action.yml with pinned full SHA commit hashes:
- `actions/checkout@v4` (lines 25 and 45) → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`
- `infracost/actions/setup@v3` (line 32) → `infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3`
Original tags are preserved as inline comments for readability.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed all four github-env-injection findings by adding newline sanitization to the write_output() helper functions in both diff/action.yml and scan/action.yml. Each write_output() function now computes `safe_value=$(printf '%s' "$value" | tr -d '\n\r')` and uses `$safe_value` instead of `$value` when writing to $GITHUB_OUTPUT. This prevents attacker-controlled inputs (version strings, PR titles, PR authors, owner, repo, PR number, repo-url, PR labels) from injecting additional key=value pairs into GITHUB_OUTPUT via embedded newlines.

### Iteration 3

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials, github-env-injection

**Notes:**

Fixed all four findings across 5 workflow files:

1. unpinned-uses: Pinned all action references to full SHA hashes with tag comments: actions/checkout@v4→11d5960a..., github/codeql-action/{init,analyze}@v3→c4dd10e..., actions/setup-go@v6→924ae3a..., actions/cache@v4→0057852..., golangci/golangci-lint-action@v9→ba0d7d2...

2. missing-permissions: Added 'permissions: {}' top-level blocks to scanner_test.yml, setup_test.yml, and verify_dist.yml.

3. hardcoded-credentials: Replaced hardcoded 'abcdefg123456' API key in setup_test.yml with '${{ secrets.INFRACOST_API_KEY }}' in the setup step input, and updated the Verify step to compare against the secret via an EXPECTED_API_KEY env var.

4. github-env-injection: Fixed write_output() in scanner_release.yml to sanitize values before writing to GITHUB_OUTPUT using 'safe_value=$(printf \'%s\' "$value" | tr -d \'\n\r\')' and writing $safe_value instead of $value.

