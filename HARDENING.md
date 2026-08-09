<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.6** was hardened automatically. 10 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references actions using mutable tags instead of pinned full-length SHA commits: `actions/checkout@v4` (lines ~24 and ~48) and `infracost/actions/setup@v3` (line ~31). These can be silently updated to point to malicious code.

Locations:

- `action.yml:24`
- `action.yml:31`
- `action.yml:48`

### unpinned-uses (severity: high)

codeql-analysis.yml references actions using mutable tags instead of pinned full-length SHA commits: `actions/checkout@v4`, `github/codeql-action/init@v3`, `github/codeql-action/analyze@v3`.

Locations:

- `.github/workflows/codeql-analysis.yml:18`
- `.github/workflows/codeql-analysis.yml:21`
- `.github/workflows/codeql-analysis.yml:25`

### unpinned-uses (severity: high)

examples_test.yml references actions using mutable tags instead of pinned full-length SHA commits: `actions/checkout@v4` (multiple occurrences).

Locations:

- `.github/workflows/examples_test.yml:14`
- `.github/workflows/examples_test.yml:19`
- `.github/workflows/examples_test.yml:23`

### unpinned-uses (severity: high)

scanner_release.yml references actions using mutable tags instead of pinned full-length SHA commits: `actions/checkout@v4` and `actions/setup-go@v6`.

Locations:

- `.github/workflows/scanner_release.yml:68`
- `.github/workflows/scanner_release.yml:70`

### unpinned-uses (severity: high)

scanner_test.yml references actions using mutable tags instead of pinned full-length SHA commits: `actions/checkout@v4`, `actions/setup-go@v6`, `actions/cache@v4`, and `golangci/golangci-lint-action@v9`.

Locations:

- `.github/workflows/scanner_test.yml:17`
- `.github/workflows/scanner_test.yml:19`
- `.github/workflows/scanner_test.yml:29`
- `.github/workflows/scanner_test.yml:31`
- `.github/workflows/scanner_test.yml:35`
- `.github/workflows/scanner_test.yml:43`
- `.github/workflows/scanner_test.yml:45`
- `.github/workflows/scanner_test.yml:52`
- `.github/workflows/scanner_test.yml:54`
- `.github/workflows/scanner_test.yml:58`

### unpinned-uses (severity: high)

setup_test.yml references actions using mutable tags instead of pinned full-length SHA commits: `actions/checkout@v4`.

Locations:

- `.github/workflows/setup_test.yml:21`

### unpinned-uses (severity: high)

verify_dist.yml references actions using mutable tags instead of pinned full-length SHA commits: `actions/checkout@v4`.

Locations:

- `.github/workflows/verify_dist.yml:18`

### missing-permissions (severity: medium)

scanner_test.yml has no top-level `permissions:` key and none of its jobs (test, integration, lint, mocks) define job-level permissions. This means the workflow runs with the default, potentially over-broad GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/scanner_test.yml:1`

### missing-permissions (severity: medium)

setup_test.yml has no top-level `permissions:` key and its only job (`test`) defines no job-level permissions. This means the workflow runs with the default, potentially over-broad GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/setup_test.yml:1`

### missing-permissions (severity: medium)

verify_dist.yml has no top-level `permissions:` key and its only job (`verify-dist`) defines no job-level permissions. This means the workflow runs with the default, potentially over-broad GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/verify_dist.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Pinned all mutable action tags to full commit SHAs across 7 files: action.yml (actions/checkout@v4 ×2, infracost/actions/setup@v3), codeql-analysis.yml (actions/checkout@v4, github/codeql-action/init@v3, github/codeql-action/analyze@v3), examples_test.yml (actions/checkout@v4 ×3), scanner_release.yml (actions/checkout@v4, actions/setup-go@v6), scanner_test.yml (actions/checkout@v4 ×4, actions/setup-go@v6 ×4, actions/cache@v4, golangci/golangci-lint-action@v9), setup_test.yml (actions/checkout@v4), verify_dist.yml (actions/checkout@v4). Added top-level `permissions: contents: read` to scanner_test.yml, setup_test.yml, and verify_dist.yml.

