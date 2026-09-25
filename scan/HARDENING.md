<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/scanner/v0.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/scanner/v0.2.1** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple ${{ ... }} expressions are directly interpolated inside run: shell command strings, allowing script injection. In the 'Derive context' step, `${{ inputs.repo-url }}` is interpolated directly into the shell script (line 72). In the 'Run scanner' step, `${{ inputs.path }}` (line 83), `${{ steps.context.outputs.repo-url }}` (line 84), and `${{ inputs.project }}` (lines 87 and 88) are all interpolated directly into the run: block. An attacker controlling these inputs can inject arbitrary shell commands. All these values must be moved to env: variables and referenced as quoted shell variables instead.

Locations:

- `action.yml:72`
- `action.yml:83`
- `action.yml:84`
- `action.yml:87`
- `action.yml:88`

### github-env-injection (severity: high)

Unsanitized user-controlled values are written to $GITHUB_OUTPUT without the required `printf '%s' ... | tr -d '\n\r'` sanitization step. (1) In the 'Determine version' step, the shell variable VERSION is sourced from `${{ inputs.version }}` (via the env: block) and written directly to $GITHUB_OUTPUT on lines 44 and 45 without sanitization — a newline in the input can inject additional key=value pairs into the output. (2) In the 'Derive context' step, REPO_URL is set directly from the `${{ inputs.repo-url }}` expression interpolated in the run: block and then written to $GITHUB_OUTPUT on line 78 without sanitization.

Locations:

- `action.yml:44`
- `action.yml:45`
- `action.yml:78`

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

Fixed all script-injection and github-env-injection findings in action.yml:
1. 'Derive context' step: moved ${{ inputs.repo-url }} to env: block as INPUT_REPO_URL; sanitized REPO_URL with printf/tr before writing to GITHUB_OUTPUT.
2. 'Run scanner' step: moved ${{ inputs.path }}, ${{ steps.context.outputs.repo-url }}, and ${{ inputs.project }} to env: block as INPUT_PATH, INPUT_REPO_URL, INPUT_PROJECT; referenced as quoted shell variables.
3. 'Determine version' step: sanitized VERSION with printf/tr before writing version and tag to GITHUB_OUTPUT.

