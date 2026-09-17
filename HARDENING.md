<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml contains three uses: references pinned to mutable version tags rather than immutable 40-character commit SHAs. This exposes the action to supply-chain attacks if the upstream tag is moved or the repository is compromised. Failing references: (1) uses: actions/checkout@v4, (2) uses: infracost/actions/setup@v3, (3) uses: actions/checkout@v4. Each should be replaced with the full SHA digest of the intended commit, e.g. actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:45`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three mutable tag references in hardened/action/action.yml to immutable commit SHAs:
1. actions/checkout@v4 → actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4 (applied to both occurrences at lines 25 and 45)
2. infracost/actions/setup@v3 → infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3 (line 31)
Original version tags are preserved as inline comments for readability.

