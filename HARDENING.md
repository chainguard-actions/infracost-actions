<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.6** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three actions by mutable version tags instead of full 40-character commit SHA pins. An attacker who compromises the upstream action repository (or creates a matching tag) could inject malicious code into any workflow that uses this action. Failing references: `actions/checkout@v4` (line 25), `infracost/actions/setup@v3` (line 32), `actions/checkout@v4` (line 46). Each should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned action references in action.yml to full 40-character commit SHAs: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 (used twice, at lines 25 and 46), and infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 (line 32). Original version tags preserved as inline comments.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed all four write_output helper functions in diff/action.yml (lines 44 and 96) and scan/action.yml (lines 22 and 74). Each function previously used a multiline heredoc-style delimiter format that was vulnerable to newline injection in user-controlled values. The fix replaces the delimiter format with a simple 'name=value' format where the value is first sanitized using `printf '%s' "$value" | tr -d '\n\r'` to strip embedded newlines and carriage returns. This prevents attacker-controlled inputs (version, github-owner, github-repo, pr-number, repo-url, pr-title, pr-author, pr-labels, pr-status) from injecting additional entries into $GITHUB_OUTPUT.

