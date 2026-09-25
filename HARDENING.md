<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three GitHub Actions using mutable tag-based refs instead of full 40-character commit SHA pins. This exposes the action to supply-chain attacks if the upstream tag is moved or the repository is compromised. Failing references: `actions/checkout@v4` (lines 24 and 46), `infracost/actions/setup@v3` (line 31). Each should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:24`
- `action.yml:31`
- `action.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned action references in hardened/action/action.yml:
1. Line 24: actions/checkout@v4 → actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4
2. Line 31: infracost/actions/setup@v3 → infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3
3. Line 46: actions/checkout@v4 → actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4
All SHAs were resolved using lookup_action_sha against the upstream repositories.

