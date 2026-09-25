<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--diff/scanner/v0.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--diff/scanner/v0.2.1** was hardened automatically. 18 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Derive context' run: block directly interpolates ${{ }} expressions into shell commands (rule a). Attacker-controlled values from inputs.* and github.* are expanded by the YAML template engine before the shell ever sees them, enabling command injection. Offending lines include: `OWNER="${{ inputs.github-owner }}"`, `REPO="${{ inputs.github-repo }}"`, `PR="${{ inputs.pr-number }}"`, `REPO_URL="${{ inputs.repo-url }}"`, `PR_STATUS="${{ inputs.pr-status }}"`, `PR="${{ github.event.pull_request.number }}"`, `PR_TITLE="${{ github.event.pull_request.title }}"`, `PR_AUTHOR="${{ github.event.pull_request.user.login }}"`, `PR_LABELS=$(echo '${{ toJson(github.event.pull_request.labels.*.name) }}' ...)`, `if [ "${{ github.event_name }}" = "pull_request" ]`, `case "${{ github.event.action }}"`, `if [ "${{ github.event.pull_request.merged }}" = "true" ]`, and `if [ -z "${{ inputs.base-path }}" ] || [ -z "${{ inputs.head-path }}" ]`. All of these must be moved to env: variables and the shell expansions must be double-quoted.

Locations:

- `action.yml:83`

### script-injection (severity: high)

The 'Run scanner' run: block directly interpolates ${{ }} expressions into shell commands (rule a). Attacker-controlled inputs.* and steps.*.outputs.* values are expanded before the shell sees them: `if [ -z "${{ inputs.base-path }}" ] || [ -z "${{ inputs.head-path }}" ]`, `--base-path "${{ inputs.base-path }}"`, `--head-path "${{ inputs.head-path }}"`, `--github-owner "${{ steps.context.outputs.owner }}"`, `--github-repo "${{ steps.context.outputs.repo }}"`, `--pr-number "${{ steps.context.outputs.pr }}"`, `--repo-url "${{ steps.context.outputs.repo-url }}"`, `--pr-title "${{ steps.context.outputs.pr-title }}"`, `--pr-author "${{ steps.context.outputs.pr-author }}"`, `--pr-labels "${{ steps.context.outputs.pr-labels }}"`, `--pipeline-run-id "${{ steps.context.outputs.pipeline-run-id }}"`, and `if [ -n "${{ inputs.project }}" ]`. These must be moved to env: variables.

Locations:

- `action.yml:133`

### script-injection (severity: high)

The 'Update PR status' run: block directly interpolates ${{ }} expressions into shell commands (rule a). steps.context.outputs.* values (which themselves derive from attacker-controlled inputs and github context) are expanded before the shell sees them: `--repo-url "${{ steps.context.outputs.repo-url }}"`, `--pr-number "${{ steps.context.outputs.pr }}"`, and `--status "${{ steps.context.outputs.pr-status }}"`.

Locations:

- `action.yml:163`

### github-env-injection (severity: high)

The 'Derive context' run: block writes values derived from untrusted inputs.* and github.* expressions to $GITHUB_OUTPUT without sanitization (no `printf '%s' ... | tr -d '\n\r'` step). Specifically: `echo "owner=${OWNER}" >> $GITHUB_OUTPUT` (OWNER from inputs.github-owner), `echo "repo=${REPO}" >> $GITHUB_OUTPUT` (REPO from inputs.github-repo), `echo "pr=${PR}" >> $GITHUB_OUTPUT` (PR from inputs.pr-number / github.event.pull_request.number), `echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT` (REPO_URL from inputs.repo-url), `echo "pr-title=${PR_TITLE}" >> $GITHUB_OUTPUT` (PR_TITLE from github.event.pull_request.title — attacker-controlled PR title), `echo "pr-author=${PR_AUTHOR}" >> $GITHUB_OUTPUT` (PR_AUTHOR from github.event.pull_request.user.login), `echo "pr-labels=${PR_LABELS}" >> $GITHUB_OUTPUT` (PR_LABELS from github.event.pull_request.labels.*.name), and `echo "pr-status=${PR_STATUS}" >> $GITHUB_OUTPUT` (PR_STATUS from inputs.pr-status). A newline in any of these values can inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `action.yml:83`

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

Fixed all three affected steps in action.yml:

1. 'Derive context' step: Added env: block with all 15 ${{ }} expressions (inputs.* and github.event.* values) moved out of the run: shell script. Shell now references plain env vars. All GITHUB_OUTPUT writes now sanitize values with `printf '%s' "$VAR" | tr -d '\n\r'` to prevent newline injection.

2. 'Run scanner' step: Added env: block with all ${{ }} expressions (inputs.base-path, inputs.head-path, inputs.project, and all steps.context.outputs.*) moved out of the run: shell script. Shell now references plain env vars.

3. 'Update PR status' step: Added env: block with steps.context.outputs.repo-url, steps.context.outputs.pr, and steps.context.outputs.pr-status moved out of the run: shell script. Shell now references plain env vars.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

In the 'Determine version' step of action.yml, added sanitization of the VERSION variable before writing to $GITHUB_OUTPUT. Added `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` and replaced the two unsanitized echo statements (lines 59-60) with sanitized versions using `safe_version`. Also properly quoted `"$GITHUB_OUTPUT"` in both echo statements.

