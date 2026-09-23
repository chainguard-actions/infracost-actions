<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml uses three `uses:` references pinned to mutable version tags rather than immutable 40-character commit SHAs. This exposes the action to supply-chain attacks if the referenced tags are moved or the upstream repositories are compromised. Failing references:
- `uses: actions/checkout@v4` (line 24)
- `uses: infracost/actions/setup@v3` (line 30)
- `uses: actions/checkout@v4` (line 44)

Each should be replaced with a full SHA pin, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:24`
- `action.yml:30`
- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three mutable tag references in hardened/action/action.yml to immutable commit SHAs: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 (used twice, lines 24 and 44), and infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 (line 30). Original version tags preserved as inline comments.

