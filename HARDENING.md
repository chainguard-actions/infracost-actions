<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.2** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses actions/checkout@v4 (twice) and infracost/actions/setup@v3 — all pinned to mutable version tags rather than full 40-character commit SHAs. A tag can be moved to point to a different (potentially malicious) commit at any time.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:46`

### script-injection (severity: high)

Sub-rule (a): ${{ ... }} expressions are interpolated directly inside run: shell command strings, allowing an attacker to inject arbitrary shell commands via PR metadata or action inputs.

action.yml 'Post Infracost comment' step: `--github-token=${{github.token}}`, `--pull-request=${{github.event.pull_request.number}}`, and `--behavior=${{inputs.behavior}}` are all interpolated directly into the shell command without going through an env: variable.

diff/action.yml 'Derive context' step: `${{ inputs.github-owner }}`, `${{ inputs.github-repo }}`, `${{ inputs.pr-number }}`, `${{ inputs.repo-url }}`, `${{ inputs.pr-status }}`, `${{ github.event.pull_request.number }}`, `${{ github.event.pull_request.title }}`, `${{ github.event.pull_request.user.login }}`, `${{ toJson(github.event.pull_request.labels.*.name) }}`, `${{ github.event_name }}`, `${{ github.event.action }}`, `${{ github.event.pull_request.merged }}`, `${{ inputs.base-path }}`, `${{ inputs.head-path }}` are all interpolated directly into the run: block.

diff/action.yml 'Run scanner' step: `${{ inputs.base-path }}`, `${{ inputs.head-path }}`, `${{ steps.context.outputs.owner }}`, `${{ steps.context.outputs.repo }}`, `${{ steps.context.outputs.pr }}`, `${{ steps.context.outputs.repo-url }}`, `${{ steps.context.outputs.pr-title }}`, `${{ steps.context.outputs.pr-author }}`, `${{ steps.context.outputs.pr-labels }}`, `${{ steps.context.outputs.pipeline-run-id }}`, `${{ inputs.project }}` are all interpolated directly.

diff/action.yml 'Update PR status' step: `${{ steps.context.outputs.repo-url }}`, `${{ steps.context.outputs.pr }}`, `${{ steps.context.outputs.pr-status }}` are all interpolated directly.

scan/action.yml 'Derive context' step: `${{ inputs.repo-url }}` is interpolated directly.

scan/action.yml 'Run scanner' step: `${{ inputs.path }}`, `${{ steps.context.outputs.repo-url }}`, `${{ inputs.project }}` are all interpolated directly.

Locations:

- `action.yml:68`
- `diff/action.yml:90`
- `diff/action.yml:152`
- `diff/action.yml:178`
- `scan/action.yml:74`
- `scan/action.yml:83`

### github-env-injection (severity: high)

Untrusted values derived from github context and inputs are written to $GITHUB_OUTPUT without sanitization (no `printf '%s' ... | tr -d '\n\r'` step).

diff/action.yml 'Derive context' step: PR_TITLE is set from `${{ github.event.pull_request.title }}` (attacker-controlled PR title), PR_AUTHOR from `${{ github.event.pull_request.user.login }}`, and PR_LABELS from `${{ toJson(github.event.pull_request.labels.*.name) }}`. All three are then written directly to $GITHUB_OUTPUT via `echo "pr-title=${PR_TITLE}" >> $GITHUB_OUTPUT`, etc., without sanitization. A newline embedded in the PR title or author login could inject arbitrary key=value pairs into GITHUB_OUTPUT, poisoning subsequent steps.

Additionally, OWNER, REPO, PR, REPO_URL, and PR_STATUS are set from inputs (e.g. `${{ inputs.github-owner }}`, `${{ inputs.pr-number }}`) and written to $GITHUB_OUTPUT without sanitization.

scan/action.yml 'Derive context' step: REPO_URL is set from `${{ inputs.repo-url }}` and written to $GITHUB_OUTPUT via `echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT` without sanitization.

Locations:

- `diff/action.yml:118`
- `diff/action.yml:119`
- `diff/action.yml:120`
- `diff/action.yml:121`
- `diff/action.yml:122`
- `diff/action.yml:123`
- `diff/action.yml:124`
- `scan/action.yml:79`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all four findings across action.yml, diff/action.yml, and scan/action.yml:

1. unpinned-uses: Pinned actions/checkout@v4 (×2) to SHA 11d5960a326750d5838078e36cf38b85af677262 and infracost/actions/setup@v3 to SHA e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832, preserving tag comments.

2. script-injection / static-inline-injection: Moved all ${{ }} expressions out of run: shell strings into env: blocks in all affected steps (action.yml 'Post Infracost comment'; diff/action.yml 'Derive context', 'Run scanner', 'Update PR status'; scan/action.yml 'Derive context', 'Run scanner'). All values are now referenced as plain $ENV_VAR shell variables.

3. github-env-injection: Added printf '%s' "$VAR" | tr -d '\n\r' sanitization before every echo >> "$GITHUB_OUTPUT" write in diff/action.yml 'Derive context' (10 outputs) and scan/action.yml 'Derive context' (1 output). Also quoted $GITHUB_OUTPUT references throughout.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in two files:
1. hardened/action/diff/action.yml: Added `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` before writing to GITHUB_OUTPUT, replacing bare `${VERSION}` with `${safe_version}` in both echo statements.
2. hardened/action/scan/action.yml: Same fix applied — sanitize VERSION with printf/tr before writing version and tag outputs to GITHUB_OUTPUT.
Both files also had $GITHUB_OUTPUT quoted properly.

