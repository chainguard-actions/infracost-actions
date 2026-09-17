<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml contains three `uses:` references pinned to mutable tags rather than full 40-character commit SHAs. Tag-based refs can be silently moved to point to different (potentially malicious) commits, enabling supply-chain attacks.

Offending references:
- `uses: actions/checkout@v4` (line ~26)
- `uses: infracost/actions/setup@v3` (line ~31)
- `uses: actions/checkout@v4` (line ~46, the second checkout step)

Locations:

- `action.yml:26`
- `action.yml:31`
- `action.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three mutable tag-based `uses:` references in hardened/action/action.yml to full 40-character commit SHAs:
- `actions/checkout@v4` (line 26) → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`
- `infracost/actions/setup@v3` (line 31) → `infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3`
- `actions/checkout@v4` (line 46) → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`
Original tags are preserved as inline comments for readability.

