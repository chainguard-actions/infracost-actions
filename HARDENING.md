<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.10

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.10** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml references three external actions using mutable version tags instead of full 40-character commit SHA digests. This exposes the action to supply-chain attacks if the referenced tags are moved or overwritten. Failing references: `actions/checkout@v4` (used twice) and `infracost/actions/setup@v3`.

Locations:

- `action.yml:24`
- `action.yml:31`
- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned action references in hardened/action/action.yml to full 40-character commit SHAs: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 (used twice, both occurrences fixed), and infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832. Original version tags preserved as inline comments.

### Iteration 2

**Fixes applied:** github-env-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all 14 findings:

1. github-env-injection (5 instances): Added `safe_value=$(printf '%s' "$value" | tr -d '\n\r')` sanitization to all write_output() helper functions in diff/action.yml (Determine version + Derive context steps), scan/action.yml (Determine version + Derive context steps), and scanner_release.yml (version job step).

2. unpinned-uses: Pinned all tag-based action refs to full SHAs:
   - actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 (in codeql-analysis.yml, examples_test.yml x3, scanner_release.yml, scanner_test.yml x4, setup_test.yml, verify_dist.yml)
   - github/codeql-action/init@v3 → @f3712979fa5f215279b101dd0a2e3bdfb4353324
   - github/codeql-action/analyze@v3 → @f3712979fa5f215279b101dd0a2e3bdfb4353324
   - actions/setup-go@v6 → @924ae3a1cded613372ab5595356fb5720e22ba16 (scanner_release.yml, scanner_test.yml x4)
   - actions/cache@v4 → @0057852bfaa89a56745cba8c7296529d2fc39830 (scanner_test.yml)
   - golangci/golangci-lint-action@v9 → @ba0d7d2ec06a0ea1cb5fa41b2e4a3ab91d21278a (scanner_test.yml)

3. missing-permissions: Added top-level `permissions: contents: read` to scanner_test.yml, setup_test.yml, and verify_dist.yml.

