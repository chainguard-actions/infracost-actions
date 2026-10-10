<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.10

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.10** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml contains three `uses:` references pinned to mutable tags rather than immutable 40-character SHA digests. This exposes the action to supply-chain attacks if the referenced tag is moved or the upstream repository is compromised. Failing references: `actions/checkout@v4` (appears twice) and `infracost/actions/setup@v3`.

Locations:

- `action.yml:24`
- `action.yml:30`
- `action.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned `uses:` references in hardened/action/action.yml to their full 40-character commit SHAs: `actions/checkout@v4` (both occurrences) → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`, and `infracost/actions/setup@v3` → `infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3`. Original tags preserved as inline comments for readability.

