<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.6** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references GitHub Actions using mutable version tags instead of pinned full-length SHA digests. This exposes the action to supply-chain attacks if the referenced tag is moved or compromised. Failing references: `actions/checkout@v4` (used twice) and `infracost/actions/setup@v3`.

Locations:

- `action.yml:24`
- `action.yml:30`
- `action.yml:47`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned action references in hardened/action/action.yml: (1) actions/checkout@v4 → actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4 (first occurrence, line 24), (2) infracost/actions/setup@v3 → infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3 (line 30), (3) actions/checkout@v4 → actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4 (second occurrence, line 47). Original tags preserved as inline comments for readability.

