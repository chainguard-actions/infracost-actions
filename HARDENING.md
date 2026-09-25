<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.2** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses tag-based (non-SHA-pinned) action references: `actions/checkout@v4` (appears twice) and `infracost/actions/setup@v3`. These should be pinned to full 40-character commit SHAs to prevent supply-chain attacks via mutable tags.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:46`

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks directly interpolate `${{ }}` expressions into shell command strings, enabling script injection. An attacker-controlled value (e.g. a PR title, branch name, or input) can break out of the shell context and execute arbitrary commands.

In `action.yml` (Post Infracost comment step, ~line 66):
  `--github-token=${{github.token}}` 
  `--pull-request=${{github.event.pull_request.number}}`
  `--behavior=${{inputs.behavior}}`

In `diff/action.yml` (Derive context step, ~line 91):
  `OWNER="${{ inputs.github-owner }}"`
  `REPO="${{ inputs.github-repo }}"`
  `PR="${{ inputs.pr-number }}"`
  `REPO_URL="${{ inputs.repo-url }}"`
  `PR_STATUS="${{ inputs.pr-status }}"`
  `PR="${{ github.event.pull_request.number }}"`
  `PR_TITLE="${{ github.event.pull_request.title }}"`
  `PR_AUTHOR="${{ github.event.pull_request.user.login }}"`
  `PR_LABELS=$(echo '${{ toJson(github.event.pull_request.labels.*.name) }}' | ...)`
  `if [ "${{ github.event_name }}" = "pull_request" ]`
  `case "${{ github.event.action }}" in`
  `if [ "${{ github.event.pull_request.merged }}" = "true" ]`
  `if [ -z "${{ inputs.base-path }}" ] || [ -z "${{ inputs.head-path }}" ]`

In `diff/action.yml` (Run scanner step, ~line 155):
  `if [ -z "${{ inputs.base-path }}" ] || [ -z "${{ inputs.head-path }}" ]`
  `--base-path "${{ inputs.base-path }}"`
  `--head-path "${{ inputs.head-path }}"`
  `--github-owner "${{ steps.context.outputs.owner }}"`
  `--github-repo "${{ steps.context.outputs.repo }}"`
  `--pr-number "${{ steps.context.outputs.pr }}"`
  `--repo-url "${{ steps.context.outputs.repo-url }}"`
  `--pr-title "${{ steps.context.outputs.pr-title }}"`
  `--pr-author "${{ steps.context.outputs.pr-author }}"`
  `--pr-labels "${{ steps.context.outputs.pr-labels }}"`
  `--pipeline-run-id "${{ steps.context.outputs.pipeline-run-id }}"`
  `if [ -n "${{ inputs.project }}" ]`
  `ARGS+=(--project "${{ inputs.project }}")`

In `diff/action.yml` (Update PR status step, ~line 185):
  `--repo-url "${{ steps.context.outputs.repo-url }}"`
  `--pr-number "${{ steps.context.outputs.pr }}"`
  `--status "${{ steps.context.outputs.pr-status }}"`

In `scan/action.yml` (Derive context step, ~line 76):
  `REPO_URL="${{ inputs.repo-url }}"`

In `scan/action.yml` (Run scanner step, ~line 86):
  `--path "${{ inputs.path }}"`
  `--repo-url "${{ steps.context.outputs.repo-url }}"`
  `if [ -n "${{ inputs.project }}" ]`
  `ARGS+=(--project "${{ inputs.project }}")`

All these should be moved to `env:` variables and referenced as `"$VAR"` in the shell.

Locations:

- `action.yml:66`
- `diff/action.yml:91`
- `diff/action.yml:155`
- `diff/action.yml:185`
- `scan/action.yml:76`
- `scan/action.yml:86`

### github-env-injection (severity: high)

Multiple `run:` blocks write values derived from untrusted inputs (`inputs.*`, `github.*`, `steps.*.outputs.*`) to `$GITHUB_OUTPUT` and `$GITHUB_PATH` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). An attacker can inject newlines into these values to smuggle additional key=value pairs into the environment files.

In `diff/action.yml` (Determine version step): writes `${VERSION}` (sourced from `${{ inputs.version }}` via env) to `$GITHUB_OUTPUT` without sanitization.

In `diff/action.yml` (Download scanner step): writes `${RUNNER_TEMP}` to `$GITHUB_PATH` — while RUNNER_TEMP is a runner-set variable, the step also uses `$VERSION` and `$TAG` from inputs/steps outputs.

In `diff/action.yml` (Derive context step): writes `OWNER`, `REPO`, `PR`, `REPO_URL`, `PR_TITLE`, `PR_AUTHOR`, `PR_LABELS`, `MODE`, `PR_STATUS` — all derived from `inputs.*` and `github.*` expressions — to `$GITHUB_OUTPUT` without sanitization. Notably `PR_TITLE` (`github.event.pull_request.title`) and `PR_AUTHOR` (`github.event.pull_request.user.login`) are fully attacker-controlled.

In `scan/action.yml` (Determine version step): writes `${VERSION}` (from `${{ inputs.version }}`) to `$GITHUB_OUTPUT` without sanitization.

In `scan/action.yml` (Download scanner step): writes `${RUNNER_TEMP}` to `$GITHUB_PATH`.

In `scan/action.yml` (Derive context step): writes `REPO_URL` (derived from `${{ inputs.repo-url }}`) to `$GITHUB_OUTPUT` without sanitization.

Locations:

- `diff/action.yml:57`
- `diff/action.yml:58`
- `diff/action.yml:84`
- `diff/action.yml:131`
- `diff/action.yml:132`
- `diff/action.yml:133`
- `diff/action.yml:134`
- `diff/action.yml:135`
- `diff/action.yml:136`
- `diff/action.yml:137`
- `diff/action.yml:138`
- `diff/action.yml:139`
- `diff/action.yml:140`
- `scan/action.yml:43`
- `scan/action.yml:44`
- `scan/action.yml:70`
- `scan/action.yml:80`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all four findings across three files:

1. **action.yml** (unpinned-uses + script-injection + static-inline-injection):
   - Pinned `actions/checkout@v4` → `@11d5960a326750d5838078e36cf38b85af677262 # v4` (both occurrences)
   - Pinned `infracost/actions/setup@v3` → `@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3`
   - Moved `${{github.token}}`, `${{github.event.pull_request.number}}`, and `${{inputs.behavior}}` from the `run:` block to `env:` variables (`GITHUB_TOKEN_VALUE`, `PR_NUMBER`, `BEHAVIOR`)

2. **diff/action.yml** (script-injection + github-env-injection):
   - Moved all `${{ inputs.* }}` and `${{ github.* }}` expressions from `run:` blocks to `env:` blocks in Derive context, Run scanner, and Update PR status steps
   - Added `printf '%s' ... | tr -d '\n\r'` sanitization for all values written to $GITHUB_OUTPUT (version, tag, owner, repo, pr, repo-url, pr-title, pr-author, pr-labels, pipeline-run-id, mode, pr-status) and $GITHUB_PATH (RUNNER_TEMP)

3. **scan/action.yml** (script-injection + github-env-injection):
   - Moved `${{ inputs.repo-url }}`, `${{ inputs.path }}`, `${{ inputs.project }}`, and `${{ steps.context.outputs.repo-url }}` from `run:` blocks to `env:` blocks
   - Added `printf '%s' ... | tr -d '\n\r'` sanitization for values written to $GITHUB_OUTPUT (version, tag, repo-url) and $GITHUB_PATH (RUNNER_TEMP)

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted `$GITHUB_REPOSITORY` expansion in the 'Post Infracost comment' step of action.yml. Changed `--repo=$GITHUB_REPOSITORY` to `--repo="$GITHUB_REPOSITORY"` to prevent shell metacharacter injection. This is consistent with how the other environment variables in the same infracost command are already properly double-quoted.

