<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.5** was hardened automatically. 10 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses unpinned action references that resolve to mutable tags rather than immutable commit SHAs, making the action vulnerable to supply-chain attacks. Unpinned refs: `actions/checkout@v4` (lines 24 and 47), `infracost/actions/setup@v3` (line 31).

Locations:

- `action.yml:24`
- `action.yml:31`
- `action.yml:47`

### unpinned-uses (severity: high)

.github/workflows/codeql-analysis.yml uses unpinned action references: `actions/checkout@v4`, `github/codeql-action/init@v3`, `github/codeql-action/analyze@v3`.

Locations:

- `.github/workflows/codeql-analysis.yml:20`
- `.github/workflows/codeql-analysis.yml:23`
- `.github/workflows/codeql-analysis.yml:27`

### unpinned-uses (severity: high)

.github/workflows/examples_test.yml uses unpinned action references: `actions/checkout@v4` (three occurrences).

Locations:

- `.github/workflows/examples_test.yml:15`
- `.github/workflows/examples_test.yml:19`
- `.github/workflows/examples_test.yml:24`

### unpinned-uses (severity: high)

.github/workflows/scanner_release.yml uses unpinned action references: `actions/checkout@v4`, `actions/setup-go@v6`.

Locations:

- `.github/workflows/scanner_release.yml:89`
- `.github/workflows/scanner_release.yml:91`

### unpinned-uses (severity: high)

.github/workflows/scanner_test.yml uses unpinned action references: `actions/checkout@v4` (×4), `actions/setup-go@v6` (×4), `actions/cache@v4`, `golangci/golangci-lint-action@v9`.

Locations:

- `.github/workflows/scanner_test.yml:19`
- `.github/workflows/scanner_test.yml:21`
- `.github/workflows/scanner_test.yml:31`
- `.github/workflows/scanner_test.yml:33`
- `.github/workflows/scanner_test.yml:37`
- `.github/workflows/scanner_test.yml:46`
- `.github/workflows/scanner_test.yml:48`
- `.github/workflows/scanner_test.yml:55`
- `.github/workflows/scanner_test.yml:57`
- `.github/workflows/scanner_test.yml:62`
- `.github/workflows/scanner_test.yml:68`
- `.github/workflows/scanner_test.yml:70`

### unpinned-uses (severity: high)

.github/workflows/setup_test.yml uses unpinned action reference: `actions/checkout@v4`.

Locations:

- `.github/workflows/setup_test.yml:18`

### unpinned-uses (severity: high)

.github/workflows/verify_dist.yml uses unpinned action reference: `actions/checkout@v4`.

Locations:

- `.github/workflows/verify_dist.yml:18`

### missing-permissions (severity: medium)

.github/workflows/scanner_test.yml has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs (test, integration, lint, mocks). Without explicit permissions, the GITHUB_TOKEN is granted default (potentially write) permissions.

Locations:

- `.github/workflows/scanner_test.yml:1`

### missing-permissions (severity: medium)

.github/workflows/setup_test.yml has no top-level `permissions:` key and no job-level `permissions:` key on its `test` job. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially write) permissions.

Locations:

- `.github/workflows/setup_test.yml:1`

### missing-permissions (severity: medium)

.github/workflows/verify_dist.yml has no top-level `permissions:` key and no job-level `permissions:` key on its `verify-dist` job. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially write) permissions.

Locations:

- `.github/workflows/verify_dist.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references by pinning to full commit SHAs: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262, infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832, github/codeql-action/init@v3 and analyze@v3 → @f3712979fa5f215279b101dd0a2e3bdfb4353324, actions/setup-go@v6 → @924ae3a1cded613372ab5595356fb5720e22ba16, actions/cache@v4 → @0057852bfaa89a56745cba8c7296529d2fc39830, golangci/golangci-lint-action@v9 → @ba0d7d2ec06a0ea1cb5fa41b2e4a3ab91d21278a. Added permissions: {} at top level and contents: read at job level for scanner_test.yml, setup_test.yml, and verify_dist.yml.

