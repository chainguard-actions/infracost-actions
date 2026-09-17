<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses three unpinned `uses:` references with mutable version tags instead of full 40-character SHA digests. This exposes the action to supply-chain attacks if the referenced tags are moved or compromised. Failing references: `actions/checkout@v4` (two occurrences) and `infracost/actions/setup@v3`.

Locations:

- `action.yml:24`
- `action.yml:31`
- `action.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned `uses:` references in hardened/action/action.yml:
1. `actions/checkout@v4` (line 24) → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`
2. `infracost/actions/setup@v3` (line 31) → `infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3`
3. `actions/checkout@v4` (line 46) → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`

All SHAs were resolved via lookup_action_sha and the original tags are preserved as inline comments.

