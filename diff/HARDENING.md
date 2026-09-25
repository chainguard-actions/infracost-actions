<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--diff/scanner/v0.2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--diff/scanner/v0.2.2** was hardened automatically. 16 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings across three steps, allowing script injection. In the 'Derive context' step, attacker-controllable values are interpolated directly: `${{ inputs.github-owner }}`, `${{ inputs.github-repo }}`, `${{ inputs.pr-number }}`, `${{ inputs.repo-url }}`, `${{ inputs.pr-status }}`, `${{ github.event.pull_request.number }}`, `${{ github.event.pull_request.title }}`, `${{ github.event.pull_request.user.login }}`, `${{ toJson(github.event.pull_request.labels.*.name) }}`, `${{ github.event_name }}`, `${{ github.event.action }}`, `${{ github.event.pull_request.merged }}`, `${{ inputs.base-path }}`, `${{ inputs.head-path }}`. In the 'Run scanner' step: `${{ inputs.base-path }}`, `${{ inputs.head-path }}`, `${{ inputs.project }}`, and multiple `${{ steps.context.outputs.* }}` values. In the 'Update PR status' step: `${{ steps.context.outputs.repo-url }}`, `${{ steps.context.outputs.pr }}`, `${{ steps.context.outputs.pr-status }}`. All of these are substituted by the Actions runner before the shell sees the script, enabling command injection via crafted input values.

Locations:

- `action.yml:57`
- `action.yml:58`
- `action.yml:59`
- `action.yml:60`
- `action.yml:61`
- `action.yml:65`
- `action.yml:79`
- `action.yml:80`
- `action.yml:81`
- `action.yml:88`
- `action.yml:89`
- `action.yml:92`
- `action.yml:100`
- `action.yml:101`
- `action.yml:115`
- `action.yml:116`
- `action.yml:122`
- `action.yml:123`
- `action.yml:124`
- `action.yml:125`
- `action.yml:126`
- `action.yml:127`
- `action.yml:128`
- `action.yml:129`
- `action.yml:132`
- `action.yml:148`
- `action.yml:149`
- `action.yml:150`

### github-env-injection (severity: high)

Multiple `run:` steps write values derived from untrusted inputs to `$GITHUB_OUTPUT` without the required sanitization (`printf '%s' ... | tr -d '\n\r'`). (1) 'Determine version' step: `VERSION` is sourced from `inputs.version` (via env var) and written unsanitized as `echo "version=${VERSION}" >> $GITHUB_OUTPUT` and `echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT`. (2) 'Derive context' step: shell variables `OWNER`, `REPO`, `PR`, `REPO_URL`, `PR_TITLE`, `PR_AUTHOR`, `PR_LABELS`, `PR_STATUS`, and `MODE` are all derived from `${{ inputs.* }}` and/or `${{ github.event.* }}` expressions and then written unsanitized to `$GITHUB_OUTPUT` (e.g. `echo "pr-title=${PR_TITLE}" >> $GITHUB_OUTPUT`, `echo "pr-author=${PR_AUTHOR}" >> $GITHUB_OUTPUT`, etc.). A newline character embedded in any of these values could inject arbitrary key=value pairs into the GitHub output context.

Locations:

- `action.yml:49`
- `action.yml:50`
- `action.yml:103`
- `action.yml:104`
- `action.yml:105`
- `action.yml:106`
- `action.yml:107`
- `action.yml:108`
- `action.yml:109`
- `action.yml:110`
- `action.yml:111`
- `action.yml:112`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.github-owner }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:93`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.github-repo }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:94`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.pr-number }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:95`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repo-url }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:96`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.pr-status }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:97`

### static-inline-injection (severity: high)

shell injection: expression "${{ github.event.pull_request.title }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:117`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.base-path }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:144`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.head-path }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:144`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.base-path }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:168`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.head-path }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:168`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.base-path }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:175`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.head-path }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:176`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.project }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:187`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.project }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:188`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, static-inline-injection

**Notes:**

Rewrote action.yml to fix all findings:

1. script-injection / static-inline-injection: Moved all ${{ ... }} expressions out of run: blocks and into env: blocks for the 'Derive context', 'Run scanner', and 'Update PR status' steps. Shell scripts now reference plain $ENV_VAR names.

2. github-env-injection: Added sanitization using `printf '%s' "$VAR" | tr -d '\n\r'` before writing all values to $GITHUB_OUTPUT. Applied to the 'Determine version' step (version, tag outputs) and the 'Derive context' step (all 10 outputs: owner, repo, pr, repo-url, pr-title, pr-author, pr-labels, pipeline-run-id, mode, pr-status).

The 'Download scanner' step was already clean (no ${{ }} in run: block, no GITHUB_OUTPUT writes). The inputs section default value for github-token retains its ${{ github.token }} expression as that is valid YAML input configuration, not a shell injection risk.

