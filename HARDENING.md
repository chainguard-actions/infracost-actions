<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.0** was hardened automatically. 5 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three external actions using mutable tag refs instead of full 40-character commit SHAs. Failing references: `actions/checkout@v4` (two occurrences) and `infracost/actions/setup@v3`. These can be silently updated by the upstream repository, enabling supply-chain attacks.

Locations:

- `action.yml:24`
- `action.yml:44`
- `action.yml:30`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in run: blocks. In action.yml's 'Post Infracost comment' step, the expressions `${{github.token}}`, `${{github.event.pull_request.number}}`, and `${{inputs.behavior}}` are interpolated directly into the shell command string. An attacker who controls the pull request can inject arbitrary shell commands via these values (e.g. a crafted PR number or behavior input containing shell metacharacters).

Locations:

- `action.yml:62`
- `action.yml:63`
- `action.yml:64`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in run: blocks in scan/action.yml. Multiple steps interpolate ${{ inputs.* }}, ${{ github.* }}, and ${{ steps.*.outputs.* }} expressions directly inside shell command strings without routing through env: variables. Affected steps and expressions include: 'Derive context' step uses `${{ inputs.github-owner }}`, `${{ inputs.github-repo }}`, `${{ inputs.pr-number }}`, `${{ inputs.commit-sha }}`, `${{ inputs.repo-url }}`, `${{ inputs.branch }}`, `${{ inputs.pr-status }}`, `${{ github.event.pull_request.number }}`, `${{ github.event_name }}`, `${{ github.event.action }}`, `${{ github.event.pull_request.merged }}`, `${{ github.event.pull_request.base.ref }}`, `${{ inputs.base-path }}`, `${{ inputs.head-path }}`; 'Run scanner' step uses `${{ inputs.base-path }}`, `${{ inputs.head-path }}`, `${{ steps.context.outputs.owner }}`, `${{ steps.context.outputs.repo }}`, `${{ steps.context.outputs.pr }}`, `${{ steps.context.outputs.commit }}`, `${{ steps.context.outputs.repo-url }}`, `${{ steps.context.outputs.branch }}`, `${{ inputs.enable-dashboard }}`, `${{ inputs.project }}`; 'Update PR status' step uses `${{ steps.context.outputs.repo-url }}`, `${{ steps.context.outputs.pr }}`, `${{ steps.context.outputs.pr-status }}`. All of these allow shell metacharacter injection.

Locations:

- `scan/action.yml:72`
- `scan/action.yml:73`
- `scan/action.yml:74`
- `scan/action.yml:75`
- `scan/action.yml:76`
- `scan/action.yml:77`
- `scan/action.yml:78`
- `scan/action.yml:130`
- `scan/action.yml:131`
- `scan/action.yml:132`
- `scan/action.yml:133`
- `scan/action.yml:134`
- `scan/action.yml:135`
- `scan/action.yml:136`
- `scan/action.yml:137`
- `scan/action.yml:155`
- `scan/action.yml:156`

### github-env-injection (severity: high)

In scan/action.yml's 'Determine version' step, the input `${{ inputs.version }}` is mapped to the env var `VERSION`, which is then written unsanitized to `$GITHUB_OUTPUT` via `echo "version=${VERSION}" >> $GITHUB_OUTPUT` and `echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT`. No `printf '%s' ... | tr -d '\n\r'` sanitization is applied before the write. A newline character in the version input could inject arbitrary key=value pairs into GITHUB_OUTPUT, poisoning subsequent steps.

In scan/action.yml's 'Derive context' step, multiple inputs (`${{ inputs.github-owner }}`, `${{ inputs.github-repo }}`, `${{ inputs.pr-number }}`, `${{ inputs.commit-sha }}`, `${{ inputs.repo-url }}`, `${{ inputs.branch }}`, `${{ inputs.pr-status }}`) are assigned to shell variables (OWNER, REPO, PR, COMMIT, REPO_URL, BRANCH, PR_STATUS) and then written unsanitized to `$GITHUB_OUTPUT` (e.g. `echo "owner=${OWNER}" >> $GITHUB_OUTPUT`). No sanitization step is applied before any of these writes.

Locations:

- `scan/action.yml:57`
- `scan/action.yml:58`
- `scan/action.yml:143`
- `scan/action.yml:144`
- `scan/action.yml:145`
- `scan/action.yml:146`
- `scan/action.yml:147`
- `scan/action.yml:148`
- `scan/action.yml:149`
- `scan/action.yml:150`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all 5 findings across action.yml and scan/action.yml:

1. **unpinned-uses**: Pinned actions/checkout@v4 (×2) to SHA 11d5960a326750d5838078e36cf38b85af677262 and infracost/actions/setup@v3 to SHA e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832, preserving tag names in comments.

2. **script-injection (action.yml)**: Moved ${{github.token}}, ${{github.event.pull_request.number}}, and ${{inputs.behavior}} from the 'Post Infracost comment' run: block into an env: block as GITHUB_TOKEN_VAL, PR_NUMBER, and BEHAVIOR.

3. **script-injection (scan/action.yml)**: Moved all ${{ inputs.* }}, ${{ github.* }}, and ${{ steps.*.outputs.* }} expressions from 'Derive context', 'Run scanner', and 'Update PR status' run: blocks into env: blocks, referencing them as plain shell variables.

4. **github-env-injection**: Added printf '%s' "$VAR" | tr -d '\n\r' sanitization before all GITHUB_OUTPUT writes in 'Determine version' (VERSION) and 'Derive context' (OWNER, REPO, PR, COMMIT, REPO_URL, BRANCH, MODE, PR_STATUS) steps.

5. **static-inline-injection**: Covered by the script-injection fix for action.yml.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted shell variable expansion in action.yml at line 70. Changed `--repo=$GITHUB_REPOSITORY` to `--repo="$GITHUB_REPOSITORY"` in the 'Post Infracost comment' step to prevent potential command injection from shell metacharacters in the repository name.

