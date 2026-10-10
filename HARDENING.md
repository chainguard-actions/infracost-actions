<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.9** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml references three external actions using mutable version tags instead of pinned full-length SHA digests. This exposes the action to supply-chain attacks where a tag could be silently moved to point to malicious code. Failing references: `actions/checkout@v4` (lines 25 and 46), `infracost/actions/setup@v3` (line 32). Each should be replaced with a full 40-character commit SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned action references in hardened/action/action.yml to full 40-character commit SHAs: actions/checkout@v4 (both occurrences on lines 25 and 46) → SHA 11d5960a326750d5838078e36cf38b85af677262, and infracost/actions/setup@v3 (line 32) → SHA e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832. Original tags preserved as inline comments.

