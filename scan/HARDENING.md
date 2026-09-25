<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/scanner/v0.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/scanner/v0.1.1** was hardened automatically. 15 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Derive context' step directly interpolates GitHub Actions expressions inside the run: shell script. Values like `${{ inputs.github-owner }}`, `${{ inputs.github-repo }}`, `${{ inputs.pr-number }}`, `${{ inputs.commit-sha }}`, `${{ inputs.repo-url }}`, `${{ inputs.branch }}`, `${{ github.event.pull_request.number }}`, and `${{ github.event.pull_request.head.ref }}` are substituted directly into the shell before execution. An attacker-controlled input containing shell metacharacters (`;`, `|`, `$(...)`, etc.) can achieve command injection.

Locations:

- `action.yml:89`
- `action.yml:90`
- `action.yml:91`
- `action.yml:92`
- `action.yml:93`
- `action.yml:94`
- `action.yml:103`
- `action.yml:117`

### script-injection (severity: high)

Sub-rule (a): The 'Run scanner' step directly interpolates GitHub Actions expressions inside the run: shell script. Values like `${{ inputs.base-path }}`, `${{ inputs.head-path }}`, `${{ inputs.project }}`, `${{ steps.context.outputs.owner }}`, `${{ steps.context.outputs.repo }}`, `${{ steps.context.outputs.pr }}`, `${{ steps.context.outputs.commit }}`, `${{ steps.context.outputs.repo-url }}`, and `${{ steps.context.outputs.branch }}` are substituted directly into the shell command array before execution. Attacker-controlled inputs can break out of the quoted string context and inject arbitrary shell commands.

Locations:

- `action.yml:134`
- `action.yml:135`
- `action.yml:136`
- `action.yml:137`
- `action.yml:138`
- `action.yml:139`
- `action.yml:140`
- `action.yml:141`
- `action.yml:144`

### github-env-injection (severity: high)

The 'Determine version' step writes the `VERSION` variable (sourced from `inputs.version` via the `VERSION` env var) directly to `$GITHUB_OUTPUT` without sanitization (`printf '%s' ... | tr -d '\n\r'`). A newline embedded in the input value could inject additional key=value pairs into GITHUB_OUTPUT, potentially overwriting subsequent outputs read by later steps.

Locations:

- `action.yml:62`
- `action.yml:63`

### github-env-injection (severity: high)

The 'Derive context' step directly interpolates `inputs.*` and `github.*` expressions into shell variables (OWNER, REPO, PR, COMMIT, REPO_URL, BRANCH) and then writes them unsanitized to `$GITHUB_OUTPUT`. No `printf '%s' ... | tr -d '\n\r'` sanitization is applied before any of the six `echo "key=${VAR}" >> $GITHUB_OUTPUT` writes. A newline in any of these attacker-controlled values can inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `action.yml:120`
- `action.yml:121`
- `action.yml:122`
- `action.yml:123`
- `action.yml:124`
- `action.yml:125`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.github-owner }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:96`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.github-repo }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:97`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.pr-number }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:98`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.commit-sha }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:99`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repo-url }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:100`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.branch }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:101`

### static-inline-injection (severity: high)

shell injection: expression "${{ github.event.pull_request.head.ref }}" appears directly in run: block of step "Derive context"; move to env: map

Locations:

- `action.yml:126`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.base-path }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:147`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.head-path }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:148`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.project }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:157`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.project }}" appears directly in run: block of step "Run scanner"; move to env: map

Locations:

- `action.yml:158`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all findings in hardened/action/action.yml:
1. 'Determine version' step: Added printf/tr sanitization of VERSION before writing to $GITHUB_OUTPUT.
2. 'Derive context' step: Added env: block with all ${{ inputs.* }} and ${{ github.event.* }} expressions (INPUT_GITHUB_OWNER, INPUT_GITHUB_REPO, INPUT_PR_NUMBER, INPUT_COMMIT_SHA, INPUT_REPO_URL, INPUT_BRANCH, EVENT_PR_NUMBER, EVENT_PR_HEAD_REF). Shell script now references plain env vars. Added printf/tr sanitization for all six values before writing to $GITHUB_OUTPUT.
3. 'Run scanner' step: Added env: block with all ${{ inputs.* }} and ${{ steps.context.outputs.* }} expressions (INPUT_BASE_PATH, INPUT_HEAD_PATH, INPUT_PROJECT, CONTEXT_OWNER, CONTEXT_REPO, CONTEXT_PR, CONTEXT_COMMIT, CONTEXT_REPO_URL, CONTEXT_BRANCH). Shell script now references plain env vars.

