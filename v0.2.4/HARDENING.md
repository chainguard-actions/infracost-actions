<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **infracost--actions/scanner/v0.2.4** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple action files and workflow files reference external actions using mutable tags instead of pinned SHA commits, making them vulnerable to supply-chain attacks. Affected references include: action.yml uses actions/checkout@v4 (lines 25, 44) and infracost/actions/setup@v3 (line 32); .github/workflows/codeql-analysis.yml uses actions/checkout@v4 (line 22), github/codeql-action/init@v3 (line 24), github/codeql-action/analyze@v3 (line 29); .github/workflows/examples_test.yml uses actions/checkout@v4 (lines 18, 24, 32); .github/workflows/scanner_release.yml uses actions/checkout@v4 and actions/setup-go@v6; .github/workflows/scanner_test.yml uses actions/checkout@v4, actions/setup-go@v6, actions/cache@v4, golangci/golangci-lint-action@v9; .github/workflows/setup_test.yml uses actions/checkout@v4; .github/workflows/verify_dist.yml uses actions/checkout@v4.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:44`
- `.github/workflows/codeql-analysis.yml:22`
- `.github/workflows/codeql-analysis.yml:24`
- `.github/workflows/codeql-analysis.yml:29`
- `.github/workflows/examples_test.yml:18`
- `.github/workflows/examples_test.yml:24`
- `.github/workflows/examples_test.yml:32`
- `.github/workflows/scanner_release.yml:82`
- `.github/workflows/scanner_release.yml:84`
- `.github/workflows/scanner_test.yml:22`
- `.github/workflows/scanner_test.yml:24`
- `.github/workflows/scanner_test.yml:33`
- `.github/workflows/scanner_test.yml:35`
- `.github/workflows/scanner_test.yml:39`
- `.github/workflows/scanner_test.yml:51`
- `.github/workflows/scanner_test.yml:53`
- `.github/workflows/scanner_test.yml:57`
- `.github/workflows/scanner_test.yml:65`
- `.github/workflows/scanner_test.yml:67`
- `.github/workflows/setup_test.yml:24`
- `.github/workflows/verify_dist.yml:22`

### missing-permissions (severity: medium)

Three workflow files have no top-level permissions: key and no job-level permissions: key on any of their jobs, meaning they run with the default (potentially broad) GITHUB_TOKEN permissions. Affected files: scanner_test.yml (jobs: test, integration, lint, mocks), setup_test.yml (job: test), verify_dist.yml (job: verify-dist).

Locations:

- `.github/workflows/scanner_test.yml:1`
- `.github/workflows/setup_test.yml:1`
- `.github/workflows/verify_dist.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Pinned all action references to full commit SHAs: actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5, infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832, github/codeql-action/init@v3 and analyze@v3 → @02c5e83432fe5497fd85b873b6c9f16a8578e1d9, actions/setup-go@v6 → @924ae3a1cded613372ab5595356fb5720e22ba16, actions/cache@v4 → @0057852bfaa89a56745cba8c7296529d2fc39830, golangci/golangci-lint-action@v9 → @ba0d7d2ec06a0ea1cb5fa41b2e4a3ab91d21278a. Added top-level `permissions: {}` and job-level `permissions: contents: read` to scanner_test.yml, setup_test.yml, and verify_dist.yml.

