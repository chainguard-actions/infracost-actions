<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.9** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags rather than full 40-character SHA digests, making the action vulnerable to supply-chain attacks if the tag is moved.

In action.yml:
- `uses: actions/checkout@v4` (line 24)
- `uses: infracost/actions/setup@v3` (line 29)
- `uses: actions/checkout@v4` (line 42)

In .github/workflows/codeql-analysis.yml:
- `uses: actions/checkout@v4`
- `uses: github/codeql-action/init@v3`
- `uses: github/codeql-action/analyze@v3`

In .github/workflows/examples_test.yml:
- `uses: actions/checkout@v4` (×3)

In .github/workflows/scanner_release.yml:
- `uses: actions/checkout@v4`
- `uses: actions/setup-go@v6`

In .github/workflows/scanner_test.yml:
- `uses: actions/checkout@v4` (×4)
- `uses: actions/setup-go@v6` (×4)
- `uses: actions/cache@v4`
- `uses: golangci/golangci-lint-action@v9`

In .github/workflows/setup_test.yml:
- `uses: actions/checkout@v4`

In .github/workflows/verify_dist.yml:
- `uses: actions/checkout@v4`

Locations:

- `action.yml:24`
- `action.yml:29`
- `action.yml:42`
- `.github/workflows/codeql-analysis.yml:17`
- `.github/workflows/codeql-analysis.yml:20`
- `.github/workflows/codeql-analysis.yml:24`
- `.github/workflows/examples_test.yml:14`
- `.github/workflows/examples_test.yml:20`
- `.github/workflows/examples_test.yml:33`
- `.github/workflows/scanner_release.yml:75`
- `.github/workflows/scanner_release.yml:77`
- `.github/workflows/scanner_test.yml:20`
- `.github/workflows/scanner_test.yml:22`
- `.github/workflows/scanner_test.yml:34`
- `.github/workflows/scanner_test.yml:36`
- `.github/workflows/scanner_test.yml:43`
- `.github/workflows/scanner_test.yml:55`
- `.github/workflows/scanner_test.yml:57`
- `.github/workflows/scanner_test.yml:62`
- `.github/workflows/scanner_test.yml:68`
- `.github/workflows/scanner_test.yml:70`
- `.github/workflows/setup_test.yml:25`
- `.github/workflows/verify_dist.yml:17`

### hardcoded-credentials (severity: high)

A literal hardcoded API key value `abcdefg123456` is assigned to the `api-key` input (matching the `api_key` pattern) in the setup_test.yml workflow. Even though this appears to be a test/dummy value, it is a non-expression literal credential assignment and violates the hardcoded-credentials check.

Locations:

- `.github/workflows/setup_test.yml:30`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` key and no job-level `permissions:` block on any of their jobs. Without explicit permissions, the GITHUB_TOKEN is granted its default (potentially broad) permissions.

- scanner_test.yml: jobs `test`, `integration`, `lint`, and `mocks` all lack permissions.
- setup_test.yml: job `test` lacks permissions.
- verify_dist.yml: job `verify-dist` lacks permissions.

Locations:

- `.github/workflows/scanner_test.yml:1`
- `.github/workflows/setup_test.yml:1`
- `.github/workflows/verify_dist.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, hardcoded-credentials, missing-permissions

**Notes:**

Fixed all findings across 7 files:

1. **unpinned-uses**: Pinned all `uses:` references to full SHA digests:
   - `actions/checkout@v4` → `@11d5960a326750d5838078e36cf38b85af677262` (in action.yml ×2, codeql-analysis.yml, examples_test.yml ×3, scanner_release.yml, scanner_test.yml ×4, setup_test.yml, verify_dist.yml)
   - `infracost/actions/setup@v3` → `@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832` (in action.yml)
   - `github/codeql-action/init@v3` → `@f3712979fa5f215279b101dd0a2e3bdfb4353324` (in codeql-analysis.yml)
   - `github/codeql-action/analyze@v3` → `@f3712979fa5f215279b101dd0a2e3bdfb4353324` (in codeql-analysis.yml)
   - `actions/setup-go@v6` → `@924ae3a1cded613372ab5595356fb5720e22ba16` (in scanner_release.yml, scanner_test.yml ×4)
   - `actions/cache@v4` → `@0057852bfaa89a56745cba8c7296529d2fc39830` (in scanner_test.yml)
   - `golangci/golangci-lint-action@v9` → `@ba0d7d2ec06a0ea1cb5fa41b2e4a3ab91d21278a` (in scanner_test.yml)

2. **hardcoded-credentials**: Replaced `api-key: abcdefg123456` with `api-key: ${{ secrets.INFRACOST_API_KEY_TEST }}` in setup_test.yml, and updated the Verify step to compare against `${{ secrets.INFRACOST_API_KEY_TEST }}` via an env var instead of the hardcoded literal.

3. **missing-permissions**: Added `permissions: contents: read` to all jobs in scanner_test.yml (test, integration, lint, mocks), setup_test.yml (test), and verify_dist.yml (verify-dist).

