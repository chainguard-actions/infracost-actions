<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.8

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.8** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml uses three `uses:` references pinned to mutable version tags instead of immutable 40-character SHA digests. This exposes the action to supply-chain attacks if the referenced tags are moved or the upstream repositories are compromised. Failing references: `actions/checkout@v4` (appears twice) and `infracost/actions/setup@v3`.

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:45`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned `uses:` references in hardened/action/action.yml to their full 40-character SHA digests: `actions/checkout@v4` (both occurrences) → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`, and `infracost/actions/setup@v3` → `infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3`. Original version tags are preserved as inline comments for readability.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed all four github-env-injection findings by updating the write_output helper function in each affected step. Added a safe_value local variable that strips newlines using `printf '%s' "$value" | tr -d '\n\r'` before writing to $GITHUB_OUTPUT. Changes applied to: (1) diff/action.yml 'Determine version' step, (2) diff/action.yml 'Derive context' step, (3) scan/action.yml 'Determine version' step, (4) scan/action.yml 'Derive context' step. The multiline delimiter format (heredoc-style) is preserved, but the value itself is now sanitized to prevent newline injection.

