<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/v4.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/v4.1.0** was hardened automatically. 11 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple ${{ ... }} expressions are interpolated directly inside run: shell command strings in action.yml's 'Post Infracost comment' step. Specifically: `--github-token=${{github.token}}`, `--pull-request=${{github.event.pull_request.number}}`, and `--behavior=${{inputs.behavior}}` are all injected directly into the shell command without going through an env: variable. An attacker controlling inputs.behavior or a PR with a crafted number could inject shell metacharacters.

Locations:

- `action.yml:67`
- `action.yml:68`
- `action.yml:69`

### script-injection (severity: high)

Sub-rule (a): The 'Derive context' step in diff/action.yml interpolates numerous ${{ inputs.* }} and ${{ github.* }} expressions directly inside the run: shell script without routing through env: variables. Offending lines include: `OWNER="${{ inputs.github-owner }}"`, `REPO="${{ inputs.github-repo }}"`, `PR="${{ inputs.pr-number }}"`, `REPO_URL="${{ inputs.repo-url }}"`, `PR_STATUS="${{ inputs.pr-status }}"`, `PR="${{ github.event.pull_request.number }}"`, `PR_TITLE="${{ github.event.pull_request.title }}"`, `PR_AUTHOR="${{ github.event.pull_request.user.login }}"`, `PR_LABELS=$(echo '${{ toJson(github.event.pull_request.labels.*.name) }}' ...)`, `if [ "${{ github.event_name }}" = ... ]`, `case "${{ github.event.action }}" in`, `if [ "${{ github.event.pull_request.merged }}" = ... ]`, and `if [ -z "${{ inputs.base-path }}" ] || [ -z "${{ inputs.head-path }}" ]`. Attacker-controlled PR metadata (title, labels, author login) can contain shell metacharacters that execute arbitrary commands.

Locations:

- `diff/action.yml:93`
- `diff/action.yml:94`
- `diff/action.yml:95`
- `diff/action.yml:96`
- `diff/action.yml:97`
- `diff/action.yml:105`
- `diff/action.yml:117`
- `diff/action.yml:118`
- `diff/action.yml:119`
- `diff/action.yml:125`
- `diff/action.yml:126`
- `diff/action.yml:129`
- `diff/action.yml:142`

### script-injection (severity: high)

Sub-rule (a): The 'Run scanner' step in diff/action.yml interpolates ${{ inputs.* }} and ${{ steps.*.outputs.* }} expressions directly inside the run: shell script. Offending lines include: `if [ -z "${{ inputs.base-path }}" ] || [ -z "${{ inputs.head-path }}" ]`, `--base-path "${{ inputs.base-path }}"`, `--head-path "${{ inputs.head-path }}"`, `--github-owner "${{ steps.context.outputs.owner }}"`, `--github-repo "${{ steps.context.outputs.repo }}"`, `--pr-number "${{ steps.context.outputs.pr }}"`, `--repo-url "${{ steps.context.outputs.repo-url }}"`, `--pr-title "${{ steps.context.outputs.pr-title }}"`, `--pr-author "${{ steps.context.outputs.pr-author }}"`, `--pr-labels "${{ steps.context.outputs.pr-labels }}"`, `--pipeline-run-id "${{ steps.context.outputs.pipeline-run-id }}"`, `if [ -n "${{ inputs.project }}" ]`, and `--project "${{ inputs.project }}"`. The step outputs themselves originate from attacker-controlled PR metadata.

Locations:

- `diff/action.yml:166`
- `diff/action.yml:173`
- `diff/action.yml:174`
- `diff/action.yml:175`
- `diff/action.yml:176`
- `diff/action.yml:177`
- `diff/action.yml:178`
- `diff/action.yml:179`
- `diff/action.yml:180`
- `diff/action.yml:181`
- `diff/action.yml:182`
- `diff/action.yml:185`
- `diff/action.yml:186`

### script-injection (severity: high)

Sub-rule (a): The 'Update PR status' step in diff/action.yml interpolates ${{ steps.context.outputs.* }} expressions directly inside the run: shell script. Offending lines: `--repo-url "${{ steps.context.outputs.repo-url }}"`, `--pr-number "${{ steps.context.outputs.pr }}"`, `--status "${{ steps.context.outputs.pr-status }}"`. These outputs originate from attacker-controlled inputs and PR metadata.

Locations:

- `diff/action.yml:199`
- `diff/action.yml:200`
- `diff/action.yml:201`

### script-injection (severity: high)

Sub-rule (a): The 'Derive context' step in scan/action.yml interpolates `${{ inputs.repo-url }}` directly inside the run: shell script: `REPO_URL="${{ inputs.repo-url }}"`. The 'Run scanner' step also interpolates `--path "${{ inputs.path }}"`, `--repo-url "${{ steps.context.outputs.repo-url }}"`, `if [ -n "${{ inputs.project }}" ]`, and `--project "${{ inputs.project }}"` directly in the shell command. Attacker-supplied inputs can contain shell metacharacters.

Locations:

- `scan/action.yml:78`
- `scan/action.yml:94`
- `scan/action.yml:95`
- `scan/action.yml:99`
- `scan/action.yml:100`

### github-env-injection (severity: high)

The 'Determine version' step in diff/action.yml sets VERSION from the env var `${{ inputs.version }}` and then writes it to $GITHUB_OUTPUT without sanitization: `echo "version=${VERSION}" >> $GITHUB_OUTPUT` and `echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT`. No `printf '%s' ... | tr -d '\n\r'` sanitization is applied before the write. A newline in inputs.version could inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `diff/action.yml:59`
- `diff/action.yml:60`

### github-env-injection (severity: high)

The 'Derive context' step in diff/action.yml writes multiple attacker-controlled values to $GITHUB_OUTPUT without sanitization. Variables OWNER, REPO, PR, REPO_URL, PR_TITLE, PR_AUTHOR, PR_LABELS, and PR_STATUS are all derived from ${{ inputs.* }} or ${{ github.event.pull_request.* }} expressions and written via unsanitized `echo "key=${VAR}" >> $GITHUB_OUTPUT` calls. PR_TITLE (from github.event.pull_request.title) and PR_AUTHOR (from github.event.pull_request.user.login) are fully attacker-controlled and can contain newlines to inject arbitrary output variables.

Locations:

- `diff/action.yml:147`
- `diff/action.yml:148`
- `diff/action.yml:149`
- `diff/action.yml:150`
- `diff/action.yml:151`
- `diff/action.yml:152`
- `diff/action.yml:153`
- `diff/action.yml:156`

### github-env-injection (severity: high)

The 'Determine version' step in scan/action.yml sets VERSION from the env var `${{ inputs.version }}` and then writes it to $GITHUB_OUTPUT without sanitization: `echo "version=${VERSION}" >> $GITHUB_OUTPUT` and `echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT`. No `printf '%s' ... | tr -d '\n\r'` sanitization is applied before the write.

Locations:

- `scan/action.yml:44`
- `scan/action.yml:45`

### github-env-injection (severity: high)

The 'Derive context' step in scan/action.yml sets REPO_URL from `${{ inputs.repo-url }}` directly in the run block and then writes it to $GITHUB_OUTPUT without sanitization: `echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT`. No `printf '%s' ... | tr -d '\n\r'` sanitization is applied. A newline in inputs.repo-url could inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `scan/action.yml:84`

### unpinned-uses (severity: high)

action.yml references three actions using mutable version tags instead of full 40-character commit SHAs, making the action vulnerable to supply-chain attacks if those tags are moved: (1) `uses: actions/checkout@v4` (line 25), (2) `uses: infracost/actions/setup@v3` (line 31), (3) `uses: actions/checkout@v4` (line 45). All three should be pinned to a specific commit SHA.

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:45`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, unpinned-uses, static-inline-injection

**Notes:**

Fixed all findings across action.yml, diff/action.yml, and scan/action.yml:

1. **unpinned-uses** (action.yml): Pinned actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 (both occurrences) and infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832.

2. **script-injection** (action.yml 'Post Infracost comment'): Moved github.token, github.event.pull_request.number, and inputs.behavior into env: block as INFRACOST_GITHUB_TOKEN, INFRACOST_PR_NUMBER, INFRACOST_BEHAVIOR; referenced as plain env vars in shell.

3. **script-injection** (diff/action.yml 'Derive context'): Moved all ${{ inputs.* }}, ${{ github.event.* }}, ${{ github.event_name }}, ${{ github.event.action }}, ${{ github.event.pull_request.merged }} into env: block; referenced as plain env vars.

4. **script-injection** (diff/action.yml 'Run scanner'): Moved all ${{ inputs.* }} and ${{ steps.context.outputs.* }} into env: block; referenced as plain env vars.

5. **script-injection** (diff/action.yml 'Update PR status'): Moved ${{ steps.context.outputs.* }} into env: block.

6. **script-injection** (scan/action.yml 'Derive context' and 'Run scanner'): Moved ${{ inputs.repo-url }}, ${{ inputs.path }}, ${{ inputs.project }}, ${{ steps.context.outputs.repo-url }} into env: blocks.

7. **github-env-injection** (diff/action.yml 'Determine version'): Added printf | tr -d newline sanitization before writing VERSION to GITHUB_OUTPUT.

8. **github-env-injection** (diff/action.yml 'Derive context'): Added printf | tr -d newline sanitization for all 10 output variables before writing to GITHUB_OUTPUT.

9. **github-env-injection** (scan/action.yml 'Determine version'): Added printf | tr -d newline sanitization before writing VERSION to GITHUB_OUTPUT.

10. **github-env-injection** (scan/action.yml 'Derive context'): Added printf | tr -d newline sanitization for REPO_URL before writing to GITHUB_OUTPUT.

### Iteration 2

**Fixes applied:** script-injection, github-env-injection, permissions, unpinned-uses

**Notes:**

Fixed all findings across 6 workflow files:

1. scanner_release.yml: Moved github.event_name, github.event.inputs.version, github.repository, and needs.version.outputs.version out of run: blocks into env: blocks. Added printf/tr sanitization before writing to GITHUB_OUTPUT. Pinned actions/checkout@v4 and actions/setup-go@v6 to full SHAs.

2. examples_test.yml: Moved github.token out of run: block into GITHUB_TOKEN env var. Pinned all 3 occurrences of actions/checkout@v4 to full SHA.

3. setup_test.yml: Moved matrix.version and secrets.GITHUB_TOKEN out of run: block into env vars. Added printf/tr sanitization before writing to GITHUB_ENV. Added top-level permissions: contents: read. Pinned actions/checkout@v4 to full SHA.

4. scanner_test.yml: Added top-level permissions: contents: read. Pinned actions/checkout@v4 (4x), actions/setup-go@v6 (3x), actions/cache@v4 (1x), and golangci/golangci-lint-action@v9 (1x) to full SHAs.

5. verify_dist.yml: Added top-level permissions: contents: read. Pinned actions/checkout@v4 to full SHA.

6. codeql-analysis.yml: Pinned actions/checkout@v4, github/codeql-action/init@v3, and github/codeql-action/analyze@v3 to full SHAs.

