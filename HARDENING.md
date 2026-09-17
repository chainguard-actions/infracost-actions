<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml references three composite action steps using mutable tag-based refs instead of pinned 40-character commit SHAs. This exposes the action to supply-chain attacks if the referenced tags are moved or overwritten. Failing references: `actions/checkout@v4` (lines 25 and 45) and `infracost/actions/setup@v3` (line 31). Each should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:45`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned action references in hardened/action/action.yml:
1. `actions/checkout@v4` (line 25) → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`
2. `infracost/actions/setup@v3` (line 31) → `infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3`
3. `actions/checkout@v4` (line 45) → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`

All SHAs were resolved using lookup_action_sha and the original tags are preserved as inline comments for readability.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed all four github-env-injection findings by replacing the unsafe write_output helper function in each affected step. The old implementation used a heredoc delimiter approach with `printf '%s\n' "$value"` which allowed newline injection. The new implementation sanitizes values with `printf '%s' "$value" | tr -d '\n\r'` before writing using the simple `name=value` format to GITHUB_OUTPUT. Changes applied to: (1) diff/action.yml 'Determine version' step, (2) diff/action.yml 'Derive context' step, (3) scan/action.yml 'Determine version' step, (4) scan/action.yml 'Derive context' step.

