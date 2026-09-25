<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/scanner/v0.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/scanner/v0.2.0** was hardened automatically. 18 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `${{ }}` expressions from attacker-controllable contexts are interpolated directly inside `run:` shell scripts (rule a), allowing shell command injection.

**"Derive context" step** — `inputs.*` and `github.*` values are interpolated directly into shell variable assignments and conditionals:
- `OWNER="${{ inputs.github-owner }}"`
- `REPO="${{ inputs.github-repo }}"`
- `PR="${{ inputs.pr-number }}"`
- `COMMIT="${{ inputs.commit-sha }}"`
- `REPO_URL="${{ inputs.repo-url }}"`
- `BRANCH="${{ inputs.branch }}"`
- `PR_STATUS="${{ inputs.pr-status }}"`
- `PR="${{ github.event.pull_request.number }}"`
- `BRANCH="${{ github.event.pull_request.base.ref }}"`
- `if [ "${{ github.event_name }}" = "pull_request" ]`
- `case "${{ github.event.action }}" in`
- `if [ "${{ github.event.pull_request.merged }}" = "true" ]`
- `if [ -z "${{ inputs.base-path }}" ] || [ -z "${{ inputs.head-path }}" ]`

**"Run scanner" step** — `inputs.*` and `steps.*.outputs.*` values interpolated directly into shell array construction:
- `if [ -z "${{ inputs.base-path }}" ] || [ -z "${{ inputs.head-path }}" ]`
- `--base-path "${{ inputs.base-path }}"`
- `--head-path "${{ inputs.head-path }}"`
- `--github-owner "${{ steps.context.outputs.owner }}"`
- `--github-repo "${{ steps.context.outputs.repo }}"`
- `--pr-number "${{ steps.context.outputs.pr }}"`
- `--commit-sha "${{ steps.context.outputs.commit }}"`
- `--repo-url "${{ steps.context.outputs.repo-url }}"`
- `--branch "${{ steps.context.outputs.branch }}"`
- `--enable-dashboard="${{ inputs.enable-dashboard }}"`
- `if [ -n "${{ inputs.project }}" ]`
- `ARGS+=(--project "${{ inputs.project }}")`

**"Update PR status" step** — `steps.*.outputs.*` values interpolated directly into shell:
- `--repo-url "${{ steps.context.outputs.repo-url }}"`
- `--pr-number "${{ steps.context.outputs.pr }}"`
- `--status "${{ steps.context.outputs.pr-status }}"`

All of these must be moved to `env:` variables and referenced as `"$VAR"` in the shell script.

Locations:

- `action.yml:92`
- `action.yml:93`
- `action.yml:94`
- `action.yml:95`
- `action.yml:96`
- `action.yml:97`
- `action.yml:98`
- `action.yml:108`
- `action.yml:120`
- `action.yml:130`
- `action.yml:136`
- `action.yml:137`
- `action.yml:155`
- `action.yml:156`
- `action.yml:163`
- `action.yml:168`
- `action.yml:169`
- `action.yml:170`
- `action.yml:171`
- `action.yml:172`
- `action.yml:173`
- `action.yml:174`
- `action.yml:175`
- `action.yml:176`
- `action.yml:180`
- `action.yml:181`
- `action.yml:188`
- `action.yml:189`
- `action.yml:190`

### github-env-injection (severity: high)

The "Determine version" and "Derive context" steps write values derived from untrusted inputs to `$GITHUB_OUTPUT` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`).

**"Determine version" step:** `VERSION` is sourced from `${{ inputs.version }}` (via the `env:` block) and written unsanitized:
- `echo "version=${VERSION}" >> $GITHUB_OUTPUT`
- `echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT`

**"Derive context" step:** `OWNER`, `REPO`, `PR`, `COMMIT`, `REPO_URL`, `BRANCH`, and `PR_STATUS` are all derived from `${{ inputs.* }}` and `${{ github.* }}` expressions and written unsanitized to `$GITHUB_OUTPUT`:
- `echo "owner=${OWNER}" >> $GITHUB_OUTPUT`
- `echo "repo=${REPO}" >> $GITHUB_OUTPUT`
- `echo "pr=${PR}" >> $GITHUB_OUTPUT`
- `echo "commit=${COMMIT}" >> $GITHUB_OUTPUT`
- `echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT`
- `echo "branch=${BRANCH}" >> $GITHUB_OUTPUT`
- `echo "mode=${MODE}" >> $GITHUB_OUTPUT`
- `echo "pr-status=${PR_STATUS}" >> $GITHUB_OUTPUT`

An attacker can inject newlines into any of these values to write arbitrary key=value pairs into `$GITHUB_OUTPUT`, potentially overwriting outputs consumed by later steps. Each write must be preceded by `safe=$(printf '%s' "$VAR" | tr -d '\n\r')`.

Locations:

- `action.yml:67`
- `action.yml:68`
- `action.yml:143`
- `action.yml:144`
- `action.yml:145`
- `action.yml:146`
- `action.yml:147`
- `action.yml:148`
- `action.yml:149`
- `action.yml:150`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.github-owner }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:103`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.github-repo }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:104`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.pr-number }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:105`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.commit-sha }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:106`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repo-url }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:107`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.branch }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:108`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.pr-status }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:109`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.base-path }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:162`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.head-path }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:162`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.base-path }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:184`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.head-path }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:184`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.base-path }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:191`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.head-path }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:192`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.enable-dashboard }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:199`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.project }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:202`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.project }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:203`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, static-inline-injection

**Notes:**

Rewrote action.yml to fix all script-injection and github-env-injection findings:

1. **script-injection / static-inline-injection**: Moved all ${{ inputs.* }} and ${{ github.* }} expressions from run: shell scripts into env: blocks for each step. The 'Derive context' step now has 14 env vars (INPUT_GITHUB_OWNER, INPUT_GITHUB_REPO, INPUT_PR_NUMBER, INPUT_COMMIT_SHA, INPUT_REPO_URL, INPUT_BRANCH, INPUT_PR_STATUS, INPUT_BASE_PATH, INPUT_HEAD_PATH, EVENT_NAME, EVENT_ACTION, EVENT_PR_NUMBER, EVENT_PR_BASE_REF, EVENT_PR_MERGED). The 'Run scanner' step has env vars for all inputs and context outputs. The 'Update PR status' step has env vars for all context outputs used.

2. **github-env-injection**: Added sanitization using `printf '%s' "$VAR" | tr -d '\n\r'` before every write to $GITHUB_OUTPUT. In 'Determine version', safe_version is sanitized before writing version= and tag= outputs. In 'Derive context', all 8 outputs (owner, repo, pr, commit, repo-url, branch, mode, pr-status) are sanitized via safe_* variables before writing.

