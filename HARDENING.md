<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.2** was hardened automatically. 9 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct ${{ }} expression interpolation inside run: shell commands. In action.yml 'Post Infracost comment' step, ${{github.token}}, ${{github.event.pull_request.number}}, and ${{inputs.behavior}} are interpolated directly into shell command arguments without routing through env: variables. An attacker controlling these values (e.g. via a crafted PR) can inject arbitrary shell commands.

Locations:

- `action.yml:67`
- `action.yml:68`
- `action.yml:69`

### script-injection (severity: high)

Sub-rule (a): Direct ${{ }} expression interpolation inside run: shell commands. In diff/action.yml 'Derive context' step, multiple untrusted expressions are interpolated directly into shell variable assignments: ${{ inputs.github-owner }}, ${{ inputs.github-repo }}, ${{ inputs.pr-number }}, ${{ inputs.repo-url }}, ${{ inputs.pr-status }}, ${{ github.event.pull_request.number }}, ${{ github.event.pull_request.title }}, ${{ github.event.pull_request.user.login }}, ${{ toJson(github.event.pull_request.labels.*.name) }}, ${{ github.event_name }}, ${{ github.event.action }}, ${{ github.event.pull_request.merged }}, ${{ inputs.base-path }}, ${{ inputs.head-path }}. These are interpolated before the shell parses the script, enabling command injection.

Locations:

- `diff/action.yml:93`

### script-injection (severity: high)

Sub-rule (a): Direct ${{ }} expression interpolation inside run: shell commands. In diff/action.yml 'Run scanner' step, untrusted expressions are interpolated directly into shell array elements: ${{ inputs.base-path }}, ${{ inputs.head-path }}, ${{ steps.context.outputs.owner }}, ${{ steps.context.outputs.repo }}, ${{ steps.context.outputs.pr }}, ${{ steps.context.outputs.repo-url }}, ${{ steps.context.outputs.pr-title }}, ${{ steps.context.outputs.pr-author }}, ${{ steps.context.outputs.pr-labels }}, ${{ steps.context.outputs.pipeline-run-id }}, ${{ inputs.project }}. These allow shell command injection via crafted input values.

Locations:

- `diff/action.yml:168`

### script-injection (severity: high)

Sub-rule (a): Direct ${{ }} expression interpolation inside run: shell commands. In diff/action.yml 'Update PR status' step, ${{ steps.context.outputs.repo-url }}, ${{ steps.context.outputs.pr }}, and ${{ steps.context.outputs.pr-status }} are interpolated directly into shell command arguments. Since these outputs are derived from untrusted inputs (github.event.pull_request.* and inputs.*), this enables command injection.

Locations:

- `diff/action.yml:193`

### script-injection (severity: high)

Sub-rule (a): Direct ${{ }} expression interpolation inside run: shell commands. In scan/action.yml 'Derive context' step, ${{ inputs.repo-url }} is interpolated directly into a shell variable assignment without routing through env:. In scan/action.yml 'Run scanner' step, ${{ inputs.path }}, ${{ steps.context.outputs.repo-url }}, and ${{ inputs.project }} are interpolated directly into shell array elements. An attacker can inject arbitrary shell commands via these inputs.

Locations:

- `scan/action.yml:75`
- `scan/action.yml:88`

### github-env-injection (severity: high)

In diff/action.yml 'Derive context' step, values derived from untrusted inputs (${{ inputs.github-owner }}, ${{ inputs.github-repo }}, ${{ inputs.pr-number }}, ${{ inputs.repo-url }}, ${{ inputs.pr-status }}, ${{ github.event.pull_request.title }}, ${{ github.event.pull_request.user.login }}, ${{ github.event.pull_request.labels.*.name }}) are assigned to shell variables and then written to $GITHUB_OUTPUT via echo without the required sanitization step (printf '%s' ... | tr -d '\n\r'). A newline in any of these values can inject arbitrary key=value pairs into GITHUB_OUTPUT, poisoning subsequent steps.

Locations:

- `diff/action.yml:155`

### github-env-injection (severity: high)

In scan/action.yml 'Derive context' step, the value of ${{ inputs.repo-url }} is assigned to REPO_URL and then written to $GITHUB_OUTPUT via 'echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT' without the required sanitization step (printf '%s' ... | tr -d '\n\r'). A newline embedded in the repo-url input can inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `scan/action.yml:75`

### unpinned-uses (severity: high)

action.yml references GitHub Actions using mutable tag refs instead of pinned full 40-character SHA commit hashes. Failing references: 'uses: actions/checkout@v4' (appears twice) and 'uses: infracost/actions/setup@v3'. These tags can be moved to point to different (potentially malicious) commits at any time, enabling supply-chain attacks.

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:45`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all 9 findings across 3 files:

1. action.yml - Pinned actions/checkout@v4 (×2) and infracost/actions/setup@v3 to full commit SHAs. Moved github.token, github.event.pull_request.number, and inputs.behavior out of run: shell into env: block for the 'Post Infracost comment' step.

2. diff/action.yml - 'Derive context' step: moved all 14 ${{ }} expressions (inputs.github-owner, inputs.github-repo, inputs.pr-number, inputs.repo-url, inputs.pr-status, inputs.base-path, inputs.head-path, github.event.pull_request.number, github.event.pull_request.title, github.event.pull_request.user.login, toJson(github.event.pull_request.labels.*.name), github.event_name, github.event.action, github.event.pull_request.merged) into env: block. Added printf/tr sanitization for all 10 values written to $GITHUB_OUTPUT. 'Run scanner' step: moved all ${{ }} expressions for inputs and context outputs into env: block. 'Update PR status' step: moved steps.context.outputs.repo-url, steps.context.outputs.pr, steps.context.outputs.pr-status into env: block.

3. scan/action.yml - 'Derive context' step: moved inputs.repo-url into env: block and added printf/tr sanitization before writing to $GITHUB_OUTPUT. 'Run scanner' step: moved inputs.path, inputs.project, and steps.context.outputs.repo-url into env: block.

### Iteration 2

**Fixes applied:** github-env-injection, script-injection, permissions, unpinned-uses

**Notes:**

Fixed all 15 findings across 7 files:

1. diff/action.yml: Sanitized VERSION with printf/tr before writing to GITHUB_OUTPUT.
2. scan/action.yml: Same sanitization fix for VERSION before GITHUB_OUTPUT.
3. scanner_release.yml: (a) Moved github.event_name and github.event.inputs.version to env block, added sanitization before GITHUB_OUTPUT write; (b) Moved all github.repository and needs.version.outputs.version expressions from run: blocks to env: blocks (GH_REPO, RELEASE_TITLE vars); (c) Pinned actions/checkout@v4 and actions/setup-go@v6 to full SHAs.
4. examples_test.yml: Moved github.token to env block as GITHUB_TOKEN; pinned all 3 actions/checkout@v4 references to SHA 11d5960a326750d5838078e36cf38b85af677262.
5. setup_test.yml: Added top-level permissions: contents: read; moved matrix.version and secrets.GITHUB_TOKEN to env block; pinned actions/checkout@v4 to full SHA.
6. scanner_test.yml: Added top-level permissions: contents: read; pinned actions/checkout@v4 (x4), actions/setup-go@v6 (x4), actions/cache@v4, and golangci/golangci-lint-action@v9 to full SHAs.
7. verify_dist.yml: Added top-level permissions: contents: read; pinned actions/checkout@v4 to full SHA.
8. codeql-analysis.yml: Pinned actions/checkout@v4, github/codeql-action/init@v3, and github/codeql-action/analyze@v3 to full SHAs.

### Iteration 3

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted shell variable expansion in the 'Post Infracost comment' step in .github/workflows/examples_test.yml. Changed `--github-token=$GITHUB_TOKEN` to `--github-token="$GITHUB_TOKEN"` to prevent shell metacharacters in the token value from being interpreted by the shell.

