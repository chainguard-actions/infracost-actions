<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.1.1** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses tag-based (non-SHA-pinned) action references, making the workflow vulnerable to supply-chain attacks if the referenced tag is moved or compromised. Failing references: `actions/checkout@v4` (line 25), `infracost/actions/setup@v3` (line 31), `actions/checkout@v4` (line 44). All should be pinned to full 40-character commit SHAs.

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:44`

### script-injection (severity: high)

Sub-rule (a): action.yml 'Post Infracost comment' step directly interpolates GitHub Actions expressions inside a run: shell command string, allowing script injection. Offending lines: `--github-token=${{github.token}}` (line 63), `--pull-request=${{github.event.pull_request.number}}` (line 64), `--behavior=${{inputs.behavior}}` (line 65). These values are substituted by the YAML template engine before the shell sees them, bypassing shell quoting.

Locations:

- `action.yml:63`
- `action.yml:64`
- `action.yml:65`

### script-injection (severity: high)

Sub-rule (a): scan/action.yml 'Derive context' step directly interpolates inputs.* and github.* expressions inside a run: shell command string. Offending lines include: `OWNER="${{ inputs.github-owner }}"` (line 93), `REPO="${{ inputs.github-repo }}"` (line 94), `PR="${{ inputs.pr-number }}"` (line 95), `COMMIT="${{ inputs.commit-sha }}"` (line 96), `REPO_URL="${{ inputs.repo-url }}"` (line 97), `BRANCH="${{ inputs.branch }}"` (line 98), `PR="${{ github.event.pull_request.number }}"` (line 103), `BRANCH="${{ github.event.pull_request.head.ref }}"` (line 115). An attacker-controlled value containing shell metacharacters will be executed by the shell.

Locations:

- `scan/action.yml:93`
- `scan/action.yml:94`
- `scan/action.yml:95`
- `scan/action.yml:96`
- `scan/action.yml:97`
- `scan/action.yml:98`
- `scan/action.yml:103`
- `scan/action.yml:115`

### script-injection (severity: high)

Sub-rule (a): scan/action.yml 'Run scanner' step directly interpolates inputs.* and steps.*.outputs.* expressions inside a run: shell command string. Offending lines include: `--base-path "${{ inputs.base-path }}"` (line 134), `--head-path "${{ inputs.head-path }}"` (line 135), `--github-owner "${{ steps.context.outputs.owner }}"` (line 136), `--github-repo "${{ steps.context.outputs.repo }}"` (line 137), `--pr-number "${{ steps.context.outputs.pr }}"` (line 138), `--commit-sha "${{ steps.context.outputs.commit }}"` (line 139), `--repo-url "${{ steps.context.outputs.repo-url }}"` (line 140), `--branch "${{ steps.context.outputs.branch }}"` (line 141), and `${{ inputs.project }}` (line 145). These are substituted before the shell parses the script, enabling command injection.

Locations:

- `scan/action.yml:134`
- `scan/action.yml:135`
- `scan/action.yml:136`
- `scan/action.yml:137`
- `scan/action.yml:138`
- `scan/action.yml:139`
- `scan/action.yml:140`
- `scan/action.yml:141`
- `scan/action.yml:145`

### github-env-injection (severity: high)

scan/action.yml 'Determine version' step writes VERSION (sourced from `${{ inputs.version }}` via the VERSION env var) to $GITHUB_OUTPUT without sanitization. The lines `echo "version=${VERSION}" >> $GITHUB_OUTPUT` and `echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT` allow a newline-containing input to inject arbitrary key=value pairs into the GitHub output context.

Locations:

- `scan/action.yml:63`
- `scan/action.yml:64`

### github-env-injection (severity: high)

scan/action.yml 'Derive context' step writes OWNER, REPO, PR, COMMIT, REPO_URL, and BRANCH (all derived from inputs.* and github.* expressions interpolated directly into the shell script) to $GITHUB_OUTPUT without sanitization. Lines `echo "owner=${OWNER}" >> $GITHUB_OUTPUT` through `echo "branch=${BRANCH}" >> $GITHUB_OUTPUT` (lines 121-126) allow newline injection to set arbitrary output variables.

Locations:

- `scan/action.yml:121`
- `scan/action.yml:122`
- `scan/action.yml:123`
- `scan/action.yml:124`
- `scan/action.yml:125`
- `scan/action.yml:126`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection

**Notes:**

Fixed all findings in action.yml and scan/action.yml:

1. unpinned-uses: Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262 (both occurrences in action.yml) and infracost/actions/setup@v3 to SHA e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832.

2. script-injection (action.yml 'Post Infracost comment'): Moved github.token, github.event.pull_request.number, and inputs.behavior into env: block as INFRACOST_GITHUB_TOKEN, INFRACOST_PR_NUMBER, INFRACOST_BEHAVIOR; referenced as shell variables in the run: block.

3. script-injection (scan/action.yml 'Derive context'): Moved all inputs.github-owner, inputs.github-repo, inputs.pr-number, inputs.commit-sha, inputs.repo-url, inputs.branch, github.event.pull_request.number, and github.event.pull_request.head.ref into env: block.

4. script-injection (scan/action.yml 'Run scanner'): Moved inputs.base-path, inputs.head-path, inputs.project, and all steps.context.outputs.* into env: block.

5. github-env-injection (scan/action.yml 'Determine version'): Added safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r') before writing to GITHUB_OUTPUT.

6. github-env-injection (scan/action.yml 'Derive context'): Added safe_* sanitization via printf/tr for all six output variables (owner, repo, pr, commit, repo-url, branch) before writing to GITHUB_OUTPUT.

