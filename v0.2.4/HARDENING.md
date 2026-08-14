<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.4** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml uses three mutable tag-based `uses:` references instead of full 40-character SHA commit digests. This exposes the action to supply-chain attacks if the referenced tags are moved or the upstream repositories are compromised.

Failing references:
- `uses: actions/checkout@v4` (line 25)
- `uses: infracost/actions/setup@v3` (line 32)
- `uses: actions/checkout@v4` (line 46)

Each should be pinned to a full SHA, e.g. `uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three mutable tag-based uses references in hardened/action/action.yml to full SHA digests:
- actions/checkout@v4 (line 25) → actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4
- infracost/actions/setup@v3 (line 32) → infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3
- actions/checkout@v4 (line 46) → actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4
All SHAs were resolved using lookup_action_sha and the original tag is preserved as a comment for readability.

### Iteration 2

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Pinned all mutable action tags to full 40-character commit SHAs: actions/checkout@v4→@11d5960a326750d5838078e36cf38b85af677262, github/codeql-action/init@v3 and analyze@v3→@f3712979fa5f215279b101dd0a2e3bdfb4353324, actions/setup-go@v6→@924ae3a1cded613372ab5595356fb5720e22ba16, actions/cache@v4→@0057852bfaa89a56745cba8c7296529d2fc39830, golangci/golangci-lint-action@v9→@ba0d7d2ec06a0ea1cb5fa41b2e4a3ab91d21278a. Added 'permissions: contents: read' to all jobs in scanner_test.yml (test, integration, lint, mocks), setup_test.yml (test), and verify_dist.yml (verify-dist). The codeql-analysis.yml and examples_test.yml already had appropriate permissions; scanner_release.yml already had top-level 'permissions: {}' with job-level overrides.

