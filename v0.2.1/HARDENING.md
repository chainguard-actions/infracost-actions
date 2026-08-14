<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.1** was hardened automatically. 10 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Post Infracost comment' run: block in action.yml directly interpolates GitHub Actions expressions inside shell commands without routing through env: variables: `--github-token=${{github.token}}`, `--pull-request=${{github.event.pull_request.number}}`, and `--behavior=${{inputs.behavior}}`. These values are template-substituted before the shell sees them, allowing an attacker to inject arbitrary shell commands via a crafted PR title, input value, or other attacker-controlled context. Sub-rule (a): direct expression interpolation in run: block.

Locations:

- `action.yml:70`
- `action.yml:71`
- `action.yml:72`

### script-injection (severity: high)

The 'Derive context' run: block in diff/action.yml directly interpolates multiple GitHub Actions expressions inside shell commands: `OWNER="${{ inputs.github-owner }}"`, `REPO="${{ inputs.github-repo }}"`, `PR="${{ inputs.pr-number }}"`, `REPO_URL="${{ inputs.repo-url }}"`, `PR_STATUS="${{ inputs.pr-status }}"`, `PR="${{ github.event.pull_request.number }}"`, `PR_TITLE="${{ github.event.pull_request.title }}"`, `PR_AUTHOR="${{ github.event.pull_request.user.login }}"`, `PR_LABELS=$(echo '${{ toJson(github.event.pull_request.labels.*.name) }}' | ...)`, `"${{ github.event_name }}"`, `"${{ github.event.action }}"`, `"${{ github.event.pull_request.merged }}"`, `"${{ inputs.base-path }}"`, `"${{ inputs.head-path }}"`. Attacker-controlled values (e.g. PR title, labels, inputs) are substituted directly into the shell script before execution. Sub-rule (a): direct expression interpolation in run: block.

Locations:

- `diff/action.yml:88`
- `diff/action.yml:89`
- `diff/action.yml:90`
- `diff/action.yml:91`
- `diff/action.yml:92`
- `diff/action.yml:100`
- `diff/action.yml:101`
- `diff/action.yml:102`

### script-injection (severity: high)

The 'Run scanner' run: block in diff/action.yml directly interpolates GitHub Actions expressions inside shell commands: `"${{ inputs.base-path }}"`, `"${{ inputs.head-path }}"`, `"${{ steps.context.outputs.owner }}"`, `"${{ steps.context.outputs.repo }}"`, `"${{ steps.context.outputs.pr }}"`, `"${{ steps.context.outputs.repo-url }}"`, `"${{ steps.context.outputs.pr-title }}"`, `"${{ steps.context.outputs.pr-author }}"`, `"${{ steps.context.outputs.pr-labels }}"`, `"${{ steps.context.outputs.pipeline-run-id }}"`, and `"${{ inputs.project }}"`. These values originate from attacker-controlled inputs and PR metadata and are substituted directly into the shell script. Sub-rule (a): direct expression interpolation in run: block.

Locations:

- `diff/action.yml:126`
- `diff/action.yml:127`
- `diff/action.yml:128`
- `diff/action.yml:129`
- `diff/action.yml:130`
- `diff/action.yml:131`
- `diff/action.yml:132`
- `diff/action.yml:133`
- `diff/action.yml:134`
- `diff/action.yml:135`
- `diff/action.yml:138`

### script-injection (severity: high)

The 'Update PR status' run: block in diff/action.yml directly interpolates GitHub Actions expressions inside shell commands: `"${{ steps.context.outputs.repo-url }}"`, `"${{ steps.context.outputs.pr }}"`, and `"${{ steps.context.outputs.pr-status }}"`. These step outputs are derived from attacker-controlled PR metadata (title, author, labels) and inputs, and are substituted directly into the shell script. Sub-rule (a): direct expression interpolation in run: block.

Locations:

- `diff/action.yml:149`
- `diff/action.yml:150`
- `diff/action.yml:151`

### script-injection (severity: high)

The 'Derive context' run: block in scan/action.yml directly interpolates `REPO_URL="${{ inputs.repo-url }}"` inside a shell command. The `inputs.repo-url` value is caller-controlled and is substituted directly into the shell script before execution. Sub-rule (a): direct expression interpolation in run: block.

Locations:

- `scan/action.yml:72`

### script-injection (severity: high)

The 'Run scanner' run: block in scan/action.yml directly interpolates GitHub Actions expressions inside shell commands: `--path "${{ inputs.path }}"`, `--repo-url "${{ steps.context.outputs.repo-url }}"`, `[ -n "${{ inputs.project }}" ]`, and `--project "${{ inputs.project }}"`. These values are caller-controlled inputs substituted directly into the shell script before execution. Sub-rule (a): direct expression interpolation in run: block.

Locations:

- `scan/action.yml:87`
- `scan/action.yml:88`
- `scan/action.yml:92`
- `scan/action.yml:93`

### github-env-injection (severity: high)

The 'Derive context' run: block in diff/action.yml writes multiple untrusted values to $GITHUB_OUTPUT without sanitization (no `printf '%s' ... | tr -d '\n\r'` step). Variables derived from attacker-controlled sources are written unsanitized: `echo "pr-title=${PR_TITLE}" >> $GITHUB_OUTPUT` (PR_TITLE from `${{ github.event.pull_request.title }}`), `echo "pr-author=${PR_AUTHOR}" >> $GITHUB_OUTPUT` (from `${{ github.event.pull_request.user.login }}`), `echo "pr-labels=${PR_LABELS}" >> $GITHUB_OUTPUT` (from `${{ toJson(github.event.pull_request.labels.*.name) }}`), and `echo "owner=${OWNER}"`, `echo "repo=${REPO}"`, `echo "pr=${PR}"`, `echo "repo-url=${REPO_URL}"`, `echo "pr-status=${PR_STATUS}"` (all from inputs). A newline in any of these values can inject arbitrary key=value pairs into the GitHub environment.

Locations:

- `diff/action.yml:109`
- `diff/action.yml:110`
- `diff/action.yml:111`
- `diff/action.yml:112`
- `diff/action.yml:113`
- `diff/action.yml:114`
- `diff/action.yml:115`

### github-env-injection (severity: high)

The 'Derive context' run: block in scan/action.yml writes `echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT` where REPO_URL is derived from `${{ inputs.repo-url }}` (a caller-controlled input) without sanitization (no `printf '%s' ... | tr -d '\n\r'` step). A newline embedded in the input value can inject arbitrary key=value pairs into the GitHub output environment.

Locations:

- `scan/action.yml:77`

### unpinned-uses (severity: high)

action.yml references three actions using mutable tag refs instead of pinned full 40-character SHA digests, making the action vulnerable to supply-chain attacks if the referenced tags are moved: `actions/checkout@v4` (line 28), `infracost/actions/setup@v3` (line 34), and `actions/checkout@v4` (line 48).

Locations:

- `action.yml:28`
- `action.yml:34`
- `action.yml:48`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, unpinned-uses, static-inline-injection

**Notes:**

Fixed all findings across action.yml, diff/action.yml, and scan/action.yml:

1. **unpinned-uses**: Pinned actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 and infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 in action.yml.

2. **script-injection** (action.yml): Moved github.token, github.event.pull_request.number, and inputs.behavior from the run: block into an env: block (GITHUB_TOKEN_VALUE, PR_NUMBER, BEHAVIOR).

3. **script-injection** (diff/action.yml - Derive context): Moved all 14 GitHub expressions into env: block variables (INPUT_GITHUB_OWNER, INPUT_GITHUB_REPO, INPUT_PR_NUMBER, INPUT_REPO_URL, INPUT_PR_STATUS, INPUT_BASE_PATH, INPUT_HEAD_PATH, EVENT_PR_NUMBER, EVENT_PR_TITLE, EVENT_PR_AUTHOR, EVENT_PR_LABELS, EVENT_NAME, EVENT_ACTION, EVENT_PR_MERGED).

4. **script-injection** (diff/action.yml - Run scanner): Moved all 11 GitHub expressions into env: block (INPUT_BASE_PATH, INPUT_HEAD_PATH, INPUT_PROJECT, CTX_OWNER, CTX_REPO, CTX_PR, CTX_REPO_URL, CTX_PR_TITLE, CTX_PR_AUTHOR, CTX_PR_LABELS, CTX_PIPELINE_RUN_ID).

5. **script-injection** (diff/action.yml - Update PR status): Moved 3 step outputs into env: block (CTX_REPO_URL, CTX_PR, CTX_PR_STATUS).

6. **script-injection** (scan/action.yml - Derive context): Moved inputs.repo-url into env: block (INPUT_REPO_URL).

7. **script-injection** (scan/action.yml - Run scanner): Moved inputs.path, steps.context.outputs.repo-url, and inputs.project into env: block (INPUT_PATH, CTX_REPO_URL, INPUT_PROJECT).

8. **github-env-injection** (diff/action.yml): Added printf '%s' ... | tr -d '\n\r' sanitization for all 10 values written to $GITHUB_OUTPUT.

9. **github-env-injection** (scan/action.yml): Added printf '%s' ... | tr -d '\n\r' sanitization for repo-url written to $GITHUB_OUTPUT.

### Iteration 2

**Fixes applied:** script-injection, github-env-injection, unpinned-uses, hardcoded-credentials, missing-permissions

**Notes:**

Fixed all 5 findings across 7 files:

1. **script-injection**: Moved all ${{ }} expressions out of run: blocks into env: blocks in scanner_release.yml (github.event_name, github.event.inputs.version, github.repository, needs.version.outputs.version), examples_test.yml (github.token), and setup_test.yml (matrix.version, secrets.GITHUB_TOKEN).

2. **github-env-injection**: Added `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` sanitization before writing to GITHUB_OUTPUT in diff/action.yml, scan/action.yml, and scanner_release.yml.

3. **unpinned-uses**: Pinned all actions to full 40-char SHA digests: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262, actions/setup-go@v6 → @924ae3a1cded613372ab5595356fb5720e22ba16, github/codeql-action/init@v3 and analyze@v3 → @f3712979fa5f215279b101dd0a2e3bdfb4353324, actions/cache@v4 → @0057852bfaa89a56745cba8c7296529d2fc39830, golangci/golangci-lint-action@v9 → @ba0d7d2ec06a0ea1cb5fa41b2e4a3ab91d21278a.

4. **hardcoded-credentials**: Replaced hardcoded `api-key: abcdefg123456` with `api-key: ${{ secrets.INFRACOST_API_KEY }}` in setup_test.yml, and updated the Verify step to compare against the secret value.

5. **missing-permissions**: Added `permissions: {}` top-level blocks to scanner_test.yml, setup_test.yml, and verify_dist.yml.

### Iteration 3

**Fixes applied:** github-env-injection, script-injection

**Notes:**

Fixed two security findings: (1) In setup_test.yml 'Get expected version' step, sanitized the INFRACOST_EXPECTED_VERSION value before writing to $GITHUB_ENV by using `safe_version=$(printf '%s' "Infracost $INFRACOST_EXPECTED_VERSION" | tr -d '\n\r')` and then writing `$safe_version` to GITHUB_ENV. (2) In examples_test.yml 'Post Infracost comment' step, quoted the $GITHUB_TOKEN variable as "$GITHUB_TOKEN" in the --github-token flag to prevent shell metacharacter injection from the github.token context value.

