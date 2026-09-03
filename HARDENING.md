<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.1.1** was hardened automatically. 7 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three actions pinned to mutable tags rather than full 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the tag is moved: `actions/checkout@v4` (two occurrences) and `infracost/actions/setup@v3`.

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:46`

### script-injection (severity: high)

Sub-rule (a): The 'Post Infracost comment' run block in action.yml directly interpolates GitHub Actions expressions inside the shell command string. The expressions `${{github.token}}`, `${{github.event.pull_request.number}}`, and `${{inputs.behavior}}` are substituted by the Actions runner before the shell sees them, allowing an attacker-controlled value (e.g. a crafted PR number or behavior string) to inject arbitrary shell commands. Offending lines:
  `--github-token=${{github.token}}`
  `--pull-request=${{github.event.pull_request.number}}`
  `--behavior=${{inputs.behavior}}`

Locations:

- `action.yml:63`
- `action.yml:64`
- `action.yml:65`

### script-injection (severity: high)

Sub-rule (a): The 'Derive context' run block in scan/action.yml directly interpolates multiple GitHub Actions expressions inside the shell command string without routing them through env vars. Attacker-controlled inputs (github-owner, github-repo, pr-number, commit-sha, repo-url, branch) and github context values (github.event.pull_request.number, github.event.pull_request.head.ref) are substituted before the shell executes, enabling shell command injection. Offending lines include:
  `OWNER="${{ inputs.github-owner }}"`
  `REPO="${{ inputs.github-repo }}"`
  `PR="${{ inputs.pr-number }}"`
  `COMMIT="${{ inputs.commit-sha }}"`
  `REPO_URL="${{ inputs.repo-url }}"`
  `BRANCH="${{ inputs.branch }}"`
  `PR="${{ github.event.pull_request.number }}"`
  `BRANCH="${{ github.event.pull_request.head.ref }}"`

Locations:

- `scan/action.yml:91`
- `scan/action.yml:92`
- `scan/action.yml:93`
- `scan/action.yml:94`
- `scan/action.yml:95`
- `scan/action.yml:96`
- `scan/action.yml:101`
- `scan/action.yml:113`

### script-injection (severity: high)

Sub-rule (a): The 'Run scanner' run block in scan/action.yml directly interpolates multiple GitHub Actions expressions inside the shell command string. Attacker-controlled inputs (base-path, head-path, project) and step outputs (steps.context.outputs.*) are substituted before the shell executes. Offending lines include:
  `--base-path "${{ inputs.base-path }}"`
  `--head-path "${{ inputs.head-path }}"`
  `--github-owner "${{ steps.context.outputs.owner }}"`
  `--github-repo "${{ steps.context.outputs.repo }}"`
  `--pr-number "${{ steps.context.outputs.pr }}"`
  `--commit-sha "${{ steps.context.outputs.commit }}"`
  `--repo-url "${{ steps.context.outputs.repo-url }}"`
  `--branch "${{ steps.context.outputs.branch }}"`
  `if [ -n "${{ inputs.project }}" ]`
  `ARGS+=(--project "${{ inputs.project }}")`

Locations:

- `scan/action.yml:130`
- `scan/action.yml:131`
- `scan/action.yml:132`
- `scan/action.yml:133`
- `scan/action.yml:134`
- `scan/action.yml:135`
- `scan/action.yml:136`
- `scan/action.yml:137`
- `scan/action.yml:139`
- `scan/action.yml:140`

### github-env-injection (severity: high)

The 'Determine version' step in scan/action.yml writes the `VERSION` variable to `$GITHUB_OUTPUT` without sanitization. `VERSION` is sourced from `${{ inputs.version }}` via the `env:` block. An attacker-controlled version string containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT, poisoning subsequent steps. The required `printf '%s' ... | tr -d '\n\r'` sanitization is absent before the writes:
  `echo "version=${VERSION}" >> $GITHUB_OUTPUT`
  `echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT`

Locations:

- `scan/action.yml:57`
- `scan/action.yml:58`

### github-env-injection (severity: high)

The 'Derive context' step in scan/action.yml writes multiple variables derived from attacker-controlled inputs (`${{ inputs.github-owner }}`, `${{ inputs.github-repo }}`, `${{ inputs.pr-number }}`, `${{ inputs.commit-sha }}`, `${{ inputs.repo-url }}`, `${{ inputs.branch }}`) and github context values (`${{ github.event.pull_request.number }}`, `${{ github.event.pull_request.head.ref }}`) to `$GITHUB_OUTPUT` without the required `printf '%s' ... | tr -d '\n\r'` sanitization. A newline-containing input value could inject arbitrary key=value pairs into GITHUB_OUTPUT. Offending writes:
  `echo "owner=${OWNER}" >> $GITHUB_OUTPUT`
  `echo "repo=${REPO}" >> $GITHUB_OUTPUT`
  `echo "pr=${PR}" >> $GITHUB_OUTPUT`
  `echo "commit=${COMMIT}" >> $GITHUB_OUTPUT`
  `echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT`
  `echo "branch=${BRANCH}" >> $GITHUB_OUTPUT`

Locations:

- `scan/action.yml:118`
- `scan/action.yml:119`
- `scan/action.yml:120`
- `scan/action.yml:121`
- `scan/action.yml:122`
- `scan/action.yml:123`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all 7 findings across action.yml and scan/action.yml:

1. **unpinned-uses**: Pinned actions/checkout@v4 (×2) to SHA 11d5960a326750d5838078e36cf38b85af677262 and infracost/actions/setup@v3 to SHA e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832.

2. **script-injection (action.yml)**: Moved github.token, github.event.pull_request.number, and inputs.behavior into env: block (GITHUB_TOKEN_VAL, PR_NUMBER, BEHAVIOR) and referenced as plain shell vars in the 'Post Infracost comment' step.

3. **script-injection (scan/action.yml - Derive context)**: Moved all 8 ${{ }} expressions (inputs.github-owner, inputs.github-repo, inputs.pr-number, inputs.commit-sha, inputs.repo-url, inputs.branch, github.event.pull_request.number, github.event.pull_request.head.ref) into env: block and referenced as plain shell vars.

4. **script-injection (scan/action.yml - Run scanner)**: Moved all 11 ${{ }} expressions (inputs.base-path, inputs.head-path, inputs.project, steps.context.outputs.*) into env: block and referenced as plain shell vars.

5. **github-env-injection (Determine version)**: Added printf/tr sanitization before writing version and tag to $GITHUB_OUTPUT.

6. **github-env-injection (Derive context)**: Added printf/tr sanitization for all 6 output variables (owner, repo, pr, commit, repo-url, branch) before writing to $GITHUB_OUTPUT.

### Iteration 2

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials, script-injection, github-env-injection

**Notes:**

Fixed all 5 findings across 6 workflow files:

1. unpinned-uses: Pinned all action references to full 40-char SHAs in codeql-analysis.yml, examples_test.yml, scanner_release.yml, scanner_test.yml, setup_test.yml, and verify_dist.yml.

2. missing-permissions: Added `permissions: contents: read` top-level block to scanner_test.yml, setup_test.yml, and verify_dist.yml.

3. hardcoded-credentials: Replaced hardcoded `api-key: abcdefg123456` with `api-key: ${{ secrets.INFRACOST_API_KEY }}` in setup_test.yml.

4. script-injection: Moved all ${{ }} expressions out of run: blocks into env: blocks in examples_test.yml (github.token), scanner_release.yml (github.event_name, github.event.inputs.version, github.repository in all jobs), and setup_test.yml (matrix.version, secrets.GITHUB_TOKEN).

5. github-env-injection: Added `printf '%s' ... | tr -d '\n\r'` sanitization before writing to $GITHUB_OUTPUT in scanner_release.yml (VERSION) and before writing to $GITHUB_ENV in setup_test.yml (INFRACOST_EXPECTED_VERSION).

### Iteration 3

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted shell expansion in the 'Post Infracost comment' step in .github/workflows/examples_test.yml. Changed `--github-token=$GITHUB_TOKEN` to `--github-token="$GITHUB_TOKEN"` to properly quote the environment variable and prevent shell metacharacter interpretation.

