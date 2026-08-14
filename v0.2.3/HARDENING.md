<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.3** was hardened automatically. 11 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

A literal hardcoded API key value `abcdefg123456` is assigned to the `api-key` input in the Setup Infracost action step. This is a non-expression literal credential, not a `${{ secrets.* }}` reference.

Locations:

- `.github/workflows/setup_test.yml:21`

### unpinned-uses (severity: high)

Multiple `uses:` references in action.yml use mutable tag-based refs instead of full 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks. Failing references: `actions/checkout@v4` (line ~24), `infracost/actions/setup@v3` (line ~29), `actions/checkout@v4` (line ~44).

Locations:

- `action.yml:24`
- `action.yml:29`
- `action.yml:44`

### unpinned-uses (severity: high)

Multiple `uses:` references in codeql-analysis.yml use mutable tag-based refs instead of full SHA commits. Failing references: `actions/checkout@v4`, `github/codeql-action/init@v3`, `github/codeql-action/analyze@v3`.

Locations:

- `.github/workflows/codeql-analysis.yml:16`
- `.github/workflows/codeql-analysis.yml:19`
- `.github/workflows/codeql-analysis.yml:23`

### unpinned-uses (severity: high)

Multiple `uses:` references in examples_test.yml use mutable tag-based refs instead of full SHA commits. Failing references: `actions/checkout@v4` (appears multiple times).

Locations:

- `.github/workflows/examples_test.yml:14`
- `.github/workflows/examples_test.yml:17`
- `.github/workflows/examples_test.yml:22`

### unpinned-uses (severity: high)

Multiple `uses:` references in scanner_release.yml use mutable tag-based refs instead of full SHA commits. Failing references: `actions/checkout@v4`, `actions/setup-go@v6`.

Locations:

- `.github/workflows/scanner_release.yml:57`
- `.github/workflows/scanner_release.yml:59`

### unpinned-uses (severity: high)

Multiple `uses:` references in scanner_test.yml use mutable tag-based refs instead of full SHA commits. Failing references: `actions/checkout@v4`, `actions/setup-go@v6`, `actions/cache@v4`, `golangci/golangci-lint-action@v9`.

Locations:

- `.github/workflows/scanner_test.yml:14`
- `.github/workflows/scanner_test.yml:16`
- `.github/workflows/scanner_test.yml:26`
- `.github/workflows/scanner_test.yml:28`
- `.github/workflows/scanner_test.yml:37`
- `.github/workflows/scanner_test.yml:39`
- `.github/workflows/scanner_test.yml:44`
- `.github/workflows/scanner_test.yml:50`
- `.github/workflows/scanner_test.yml:52`

### unpinned-uses (severity: high)

The `uses:` reference in setup_test.yml uses a mutable tag-based ref instead of a full SHA commit. Failing reference: `actions/checkout@v4`.

Locations:

- `.github/workflows/setup_test.yml:16`

### unpinned-uses (severity: high)

The `uses:` reference in verify_dist.yml uses a mutable tag-based ref instead of a full SHA commit. Failing reference: `actions/checkout@v4`.

Locations:

- `.github/workflows/verify_dist.yml:16`

### missing-permissions (severity: medium)

scanner_test.yml has no top-level `permissions:` key and none of its jobs (`test`, `integration`, `lint`, `mocks`) define job-level `permissions:`. This means the workflow runs with the default (potentially broad) GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/scanner_test.yml:1`

### missing-permissions (severity: medium)

setup_test.yml has no top-level `permissions:` key and the `test` job has no job-level `permissions:` block. This means the workflow runs with the default (potentially broad) GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/setup_test.yml:1`

### missing-permissions (severity: medium)

verify_dist.yml has no top-level `permissions:` key and the `verify-dist` job has no job-level `permissions:` block. This means the workflow runs with the default (potentially broad) GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/verify_dist.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, unpinned-uses, missing-permissions

**Notes:**

Fixed all 11 findings across 7 files:

1. hardcoded-credentials (setup_test.yml line 21): Replaced literal `abcdefg123456` API key with `${{ secrets.INFRACOST_TEST_API_KEY }}` in both the Setup infracost step and the Verify step comparison.

2. unpinned-uses (action.yml): Pinned `actions/checkout@v4` → `@11d5960a326750d5838078e36cf38b85af677262` (×2) and `infracost/actions/setup@v3` → `@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832`.

3. unpinned-uses (codeql-analysis.yml): Pinned `actions/checkout@v4` → `@11d5960a326750d5838078e36cf38b85af677262`, `github/codeql-action/init@v3` and `github/codeql-action/analyze@v3` → `@f3712979fa5f215279b101dd0a2e3bdfb4353324`.

4. unpinned-uses (examples_test.yml): Pinned all 3 occurrences of `actions/checkout@v4` → `@11d5960a326750d5838078e36cf38b85af677262`.

5. unpinned-uses (scanner_release.yml): Pinned `actions/checkout@v4` → `@11d5960a326750d5838078e36cf38b85af677262` and `actions/setup-go@v6` → `@924ae3a1cded613372ab5595356fb5720e22ba16`.

6. unpinned-uses (scanner_test.yml): Pinned `actions/checkout@v4` (×4), `actions/setup-go@v6` (×4), `actions/cache@v4` → `@0057852bfaa89a56745cba8c7296529d2fc39830`, and `golangci/golangci-lint-action@v9` → `@ba0d7d2ec06a0ea1cb5fa41b2e4a3ab91d21278a`.

7. unpinned-uses (setup_test.yml): Pinned `actions/checkout@v4` → `@11d5960a326750d5838078e36cf38b85af677262`.

8. unpinned-uses (verify_dist.yml): Pinned `actions/checkout@v4` → `@11d5960a326750d5838078e36cf38b85af677262`.

9. missing-permissions (scanner_test.yml): Added `permissions: {}` at top level.

10. missing-permissions (setup_test.yml): Added `permissions: {}` at top level.

11. missing-permissions (verify_dist.yml): Added `permissions: {}` at top level.

