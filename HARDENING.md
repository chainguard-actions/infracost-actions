<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.5** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml references three composite action steps using mutable version tags instead of full 40-character commit SHAs. This exposes the action to supply-chain attacks where a tag could be silently moved to a different (potentially malicious) commit. Failing references:
- `uses: actions/checkout@v4` (Checkout base branch step)
- `uses: infracost/actions/setup@v3` (Setup Infracost step)
- `uses: actions/checkout@v4` (anonymous checkout step)
All three should be pinned to their full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned action references in hardened/action/action.yml:
- `actions/checkout@v4` → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4` (applied to both the 'Checkout base branch' step and the anonymous checkout step)
- `infracost/actions/setup@v3` → `infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3`
All SHAs were resolved via lookup_action_sha.

