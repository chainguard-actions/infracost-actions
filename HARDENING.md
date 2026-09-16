<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three external actions using mutable version tags instead of full 40-character commit SHA digests. This exposes the action to supply-chain attacks if the referenced tag is moved or the upstream repository is compromised. Failing references: `uses: actions/checkout@v4` (line 25), `uses: infracost/actions/setup@v3` (line 32), and `uses: actions/checkout@v4` (line 43). Each should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:43`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all three unpinned action references in hardened/action/action.yml to full 40-character commit SHAs: (1) actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 # v4 (applied to both occurrences at lines 25 and 43), and (2) infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3 (line 32). Original version tags preserved as inline comments for readability.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed all four github-env-injection findings by adding newline sanitization to the write_output function in each affected step:
1. diff/action.yml 'Determine version' step: Added `safe_value=$(printf '%s' "$value" | tr -d '\n\r')` and use `$safe_value` when writing to $GITHUB_OUTPUT.
2. diff/action.yml 'Derive context' step: Same fix applied to the write_output function that writes OWNER, REPO, PR, REPO_URL, PR_TITLE, PR_AUTHOR, PR_LABELS, and PR_STATUS.
3. scan/action.yml 'Determine version' step: Same fix applied to sanitize VERSION before writing.
4. scan/action.yml 'Derive context' step: Same fix applied to sanitize REPO_URL before writing.
All write_output functions now strip carriage returns and newlines from values before writing them to $GITHUB_OUTPUT, preventing injection of additional key=value pairs via attacker-controlled inputs.

