<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **infracost--actions/scanner/v0.2.3** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml contains three uses: references pinned to mutable tags instead of immutable 40-character commit SHAs. This exposes the action to supply-chain attacks if the referenced tag is moved or the upstream repository is compromised. Failing references: 'actions/checkout@v4' (lines 25 and 44), 'infracost/actions/setup@v3' (line 31).

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three mutable tag references in action.yml to immutable 40-character commit SHAs: (1) actions/checkout@v4 at line 25 → @34e114876b0b11c390a56381ad16ebd13914f8d5 # v4, (2) infracost/actions/setup@v3 at line 31 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3, (3) actions/checkout@v4 at line 44 → @34e114876b0b11c390a56381ad16ebd13914f8d5 # v4. Original tag names preserved as inline comments for readability.

### Iteration 2

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials, github-env-injection

**Notes:**

Fixed all findings:

1. unpinned-uses: Pinned all action references to full commit SHAs:
   - actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 (in codeql-analysis.yml, examples_test.yml, scanner_release.yml, scanner_test.yml, setup_test.yml, verify_dist.yml)
   - github/codeql-action/init@v3 → @02c5e83432fe5497fd85b873b6c9f16a8578e1d9
   - github/codeql-action/analyze@v3 → @02c5e83432fe5497fd85b873b6c9f16a8578e1d9
   - actions/setup-go@v6 → @924ae3a1cded613372ab5595356fb5720e22ba16 (in scanner_release.yml, scanner_test.yml)
   - actions/cache@v4 → @0057852bfaa89a56745cba8c7296529d2fc39830
   - golangci/golangci-lint-action@v9 → @ba0d7d2ec06a0ea1cb5fa41b2e4a3ab91d21278a

2. missing-permissions: Added `permissions: {}` top-level block to scanner_test.yml, setup_test.yml, and verify_dist.yml.

3. hardcoded-credentials: Replaced hardcoded `api-key: abcdefg123456` in setup_test.yml with `api-key: ${{ secrets.INFRACOST_API_KEY }}`. Updated the Verify step to compare against the secret value via an env var instead of the literal string.

4. github-env-injection: Fixed all write_output() helper functions in diff/action.yml (Determine version + Derive context steps), scan/action.yml (Determine version + Derive context steps), and scanner_release.yml (version step) to sanitize values with `safe_value=$(printf '%s' "$value" | tr -d '\n\r')` before writing to $GITHUB_OUTPUT.

