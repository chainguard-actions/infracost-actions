<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/scanner/v0.2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/scanner/v0.2.2** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple ${{ }} expressions are directly interpolated inside run: shell command strings, violating sub-rule (a). In the 'Derive context' step, `REPO_URL="${{ inputs.repo-url }}"` (line 69) embeds the user-controlled input directly into the shell script before the shell ever sees it, enabling command injection. In the 'Run scanner' step, four expressions are interpolated directly: `--path "${{ inputs.path }}"` (line 82), `--repo-url "${{ steps.context.outputs.repo-url }}"` (line 83), `if [ -n "${{ inputs.project }}" ]` (line 86), and `ARGS+=(--project "${{ inputs.project }}")` (line 87). All of these should be moved to env: variables and referenced as quoted shell variables instead.

Locations:

- `action.yml:69`
- `action.yml:82`
- `action.yml:83`
- `action.yml:86`
- `action.yml:87`

### github-env-injection (severity: high)

Untrusted input values are written to special GitHub environment files without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). (1) In the 'Determine version' step, the shell variable $VERSION is sourced from `inputs.version` (via the env: block) and written unsanitized to $GITHUB_OUTPUT on lines 44 and 45: `echo "version=${VERSION}" >> $GITHUB_OUTPUT` and `echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT`. (2) In the 'Derive context' step, $REPO_URL is set directly from `${{ inputs.repo-url }}` interpolated in the run: block (line 69) and then written unsanitized to $GITHUB_OUTPUT on line 74: `echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT`. A newline injected into any of these values could allow an attacker to set arbitrary environment variables or outputs.

Locations:

- `action.yml:44`
- `action.yml:45`
- `action.yml:74`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repo-url }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:78`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.path }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:94`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.project }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:99`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.project }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:100`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all findings in action.yml:
1. 'Derive context' step: moved inputs.repo-url from inline run: to env: block (INPUT_REPO_URL), and sanitized REPO_URL with tr -d '\n\r' before writing to GITHUB_OUTPUT.
2. 'Run scanner' step: moved inputs.path, steps.context.outputs.repo-url, and inputs.project from inline run: to env: block (INPUT_PATH, INPUT_REPO_URL, INPUT_PROJECT), then referenced as shell variables.
3. 'Determine version' step: sanitized VERSION with tr -d '\n\r' before writing version and tag to GITHUB_OUTPUT.

