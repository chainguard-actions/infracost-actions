<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.4** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml contains three `uses:` references pinned to mutable version tags rather than immutable 40-character commit SHAs. This exposes the action to supply-chain attacks if the upstream tag is moved or the repository is compromised. Failing references: `actions/checkout@v4` (line 24), `infracost/actions/setup@v3` (line 30), `actions/checkout@v4` (line 44). Each should be replaced with the corresponding full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:24`
- `action.yml:30`
- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three mutable tag references in hardened/action/action.yml to full commit SHAs: (1) actions/checkout@v4 (line 24) → actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4, (2) infracost/actions/setup@v3 (line 30) → infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3, (3) actions/checkout@v4 (line 44) → actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4. All SHAs were resolved via lookup_action_sha.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed all four github-env-injection findings by updating the write_output() function in both diff/action.yml and scan/action.yml. In each file, both the 'Determine version' step and the 'Derive context' step had their own copy of write_output(). The old implementation used a multiline heredoc delimiter format with `printf '%s\n' "$value"` without sanitization. The new implementation sanitizes the value first with `safe_value=$(printf '%s' "$value" | tr -d '\n\r')` and then writes using the simple `printf '%s=%s\n' "$name" "$safe_value"` format, preventing embedded newlines from injecting additional output variables into $GITHUB_OUTPUT.

