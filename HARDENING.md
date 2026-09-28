<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/v4.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/v4.2.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three GitHub Actions steps using mutable version tags instead of full 40-character commit SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved or compromised. Failing references: `actions/checkout@v4` (line 25), `infracost/actions/setup@v3` (line 32), `actions/checkout@v4` (line 44).

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned action references in hardened/action/action.yml to their full 40-character commit SHAs: (1) actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 # v4 (applied to both occurrences on lines 25 and 44), (2) infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3. Original version tags preserved as inline comments for readability.

