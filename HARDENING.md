<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/v5.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/v5.0.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three Actions using mutable version tags instead of pinned 40-character SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved or hijacked:
- `uses: actions/checkout@v4` (line 25, Checkout base branch step)
- `uses: infracost/actions/setup@v3` (line 30, Setup Infracost step)
- `uses: actions/checkout@v4` (line 43, unnamed checkout step)
All three should be pinned to a full commit SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:25`
- `action.yml:30`
- `action.yml:43`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned action references in hardened/action/action.yml to full 40-character commit SHAs: (1) actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 # v4 (applied to both occurrences on lines 25 and 43), (2) infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3. Original version tags preserved as inline comments.

