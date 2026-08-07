<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.8

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.8** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references across action.yml and workflow files are pinned to mutable version tags rather than immutable 40-character SHA commit hashes, making them vulnerable to supply-chain attacks.

action.yml: `uses: actions/checkout@v4` (×2), `uses: infracost/actions/setup@v3`
.github/workflows/codeql-analysis.yml: `uses: actions/checkout@v4`, `uses: github/codeql-action/init@v3`, `uses: github/codeql-action/analyze@v3`
.github/workflows/examples_test.yml: `uses: actions/checkout@v4` (×3)
.github/workflows/scanner_release.yml: `uses: actions/checkout@v4`, `uses: actions/setup-go@v6`
.github/workflows/scanner_test.yml: `uses: actions/checkout@v4` (×4), `uses: actions/setup-go@v6` (×4), `uses: actions/cache@v4`, `uses: golangci/golangci-lint-action@v9`
.github/workflows/setup_test.yml: `uses: actions/checkout@v4`
.github/workflows/verify_dist.yml: `uses: actions/checkout@v4`

Locations:

- `action.yml:24`
- `action.yml:29`
- `action.yml:46`
- `.github/workflows/codeql-analysis.yml:18`
- `.github/workflows/codeql-analysis.yml:21`
- `.github/workflows/codeql-analysis.yml:26`
- `.github/workflows/examples_test.yml:16`
- `.github/workflows/examples_test.yml:21`
- `.github/workflows/examples_test.yml:30`
- `.github/workflows/scanner_release.yml:82`
- `.github/workflows/scanner_release.yml:84`
- `.github/workflows/scanner_test.yml:18`
- `.github/workflows/scanner_test.yml:20`
- `.github/workflows/scanner_test.yml:30`
- `.github/workflows/scanner_test.yml:32`
- `.github/workflows/scanner_test.yml:36`
- `.github/workflows/scanner_test.yml:46`
- `.github/workflows/scanner_test.yml:48`
- `.github/workflows/scanner_test.yml:54`
- `.github/workflows/scanner_test.yml:62`
- `.github/workflows/scanner_test.yml:64`
- `.github/workflows/setup_test.yml:25`
- `.github/workflows/verify_dist.yml:19`

### hardcoded-credentials (severity: high)

A literal hardcoded API key value `abcdefg123456` is assigned to `api-key:` in the setup_test.yml workflow. This is a non-expression literal value (not a `${{ secrets.* }}` reference) assigned to a name containing 'key'. Even if used for testing purposes, hardcoded credentials in workflow files are a security risk as they are committed to the repository.

Locations:

- `.github/workflows/setup_test.yml:31`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, workflows inherit the default repository permissions (which may be broad), violating the principle of least privilege.

- scanner_test.yml: jobs `test`, `integration`, `lint`, `mocks` — none have permissions defined
- setup_test.yml: job `test` — no permissions defined
- verify_dist.yml: job `verify-dist` — no permissions defined

Locations:

- `.github/workflows/scanner_test.yml:1`
- `.github/workflows/setup_test.yml:1`
- `.github/workflows/verify_dist.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, hardcoded-credentials, missing-permissions

**Notes:**

Fixed all findings:

1. **unpinned-uses**: Pinned all `uses:` references to full 40-char SHAs in action.yml, codeql-analysis.yml, examples_test.yml, scanner_release.yml, scanner_test.yml, setup_test.yml, and verify_dist.yml. All tags preserved as inline comments.

2. **hardcoded-credentials**: Replaced `api-key: abcdefg123456` in setup_test.yml with `api-key: ${{ secrets.INFRACOST_TEST_API_KEY }}`.

3. **missing-permissions**: Added `permissions: {}` top-level blocks to scanner_test.yml, setup_test.yml, and verify_dist.yml.

Remaining `@v` references in README.md, CONTRIBUTING.md, diff/README.md, scan/README.md, setup/README.md, and testdata/ are documentation examples and test harness files — not part of the distributed action.

