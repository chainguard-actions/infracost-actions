<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.9** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml uses three `uses:` references pinned to mutable version tags rather than immutable 40-character commit SHAs. This exposes the action to supply-chain attacks if the referenced tags are moved or the upstream repositories are compromised.

Failing references:
- `uses: actions/checkout@v4` (line 25)
- `uses: infracost/actions/setup@v3` (line 32)
- `uses: actions/checkout@v4` (line 46)

Each should be replaced with a full SHA pin, e.g. `uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three mutable `uses:` references in hardened/action/action.yml:
- `actions/checkout@v4` (lines 25 and 46) → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`
- `infracost/actions/setup@v3` (line 32) → `infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3`
Original tags preserved as inline comments for readability.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed all four github-env-injection findings by updating the write_output helper function in each affected step:

1. diff/action.yml 'Determine version' step (line 44): Added `safe_value="$(printf '%s' "$value" | tr -d '\n\r')"` and used `safe_value` in the printf that writes to $GITHUB_OUTPUT.

2. diff/action.yml 'Derive context' step (line 100): Same fix applied to the write_output helper, protecting PR_TITLE, PR_AUTHOR, PR_LABELS, OWNER, REPO, PR, and REPO_URL.

3. scan/action.yml 'Determine version' step (line 26): Same fix applied to protect the VERSION value.

4. scan/action.yml 'Derive context' step (line 75): Same fix applied to protect the REPO_URL value.

The sanitization uses a two-step approach (capture raw value first, then sanitize) to avoid masking command failures under errexit, as required by the guidelines.

