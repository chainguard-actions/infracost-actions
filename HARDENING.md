<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references GitHub Actions using mutable version tags instead of pinned full-length SHA digests, making the action vulnerable to supply-chain attacks if the referenced tag is moved or compromised. Failing references: `uses: actions/checkout@v4` (lines 25 and 46), `uses: infracost/actions/setup@v3` (line 31). Each should be replaced with a full 40-character commit SHA, e.g. `uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned action references in hardened/action/action.yml to their full 40-character commit SHAs: (1) actions/checkout@v4 (line 25) → @11d5960a326750d5838078e36cf38b85af677262 # v4, (2) infracost/actions/setup@v3 (line 31) → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3, (3) actions/checkout@v4 (line 46) → @11d5960a326750d5838078e36cf38b85af677262 # v4. Original tags preserved as inline comments for readability.

