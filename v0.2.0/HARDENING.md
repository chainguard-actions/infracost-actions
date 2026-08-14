<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.0** was hardened automatically. 8 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses unpinned (tag-based) `uses:` references instead of full 40-character commit SHAs. Failing references: `actions/checkout@v4` (line 25), `infracost/actions/setup@v3` (line 31), `actions/checkout@v4` (line 45). These are mutable tags that can be silently updated to point to different (potentially malicious) code.

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:45`

### script-injection (severity: high)

Rule (a): action.yml 'Post Infracost comment' step directly interpolates GitHub Actions expressions inside a `run:` shell command. The expressions `${{github.token}}`, `${{github.event.pull_request.number}}`, and `${{inputs.behavior}}` are substituted into the shell string before the shell parses it, enabling command injection if any value contains shell metacharacters. Offending lines: `--github-token=${{github.token}}`, `--pull-request=${{github.event.pull_request.number}}`, `--behavior=${{inputs.behavior}}`.

Locations:

- `action.yml:57`

### script-injection (severity: high)

Rule (a): scan/action.yml 'Derive context' step directly interpolates multiple `inputs.*` and `github.*` expressions inside a `run:` shell script. Affected expressions include: `${{ inputs.github-owner }}`, `${{ inputs.github-repo }}`, `${{ inputs.pr-number }}`, `${{ inputs.commit-sha }}`, `${{ inputs.repo-url }}`, `${{ inputs.branch }}`, `${{ inputs.pr-status }}`, `${{ github.event.pull_request.number }}`, `${{ github.event.pull_request.base.ref }}`, `${{ github.event_name }}`, `${{ github.event.action }}`, `${{ github.event.pull_request.merged }}`, `${{ inputs.base-path }}`, `${{ inputs.head-path }}`. These are substituted into the shell before parsing, allowing command injection via attacker-controlled input values.

Locations:

- `scan/action.yml:57`

### script-injection (severity: high)

Rule (a): scan/action.yml 'Run scanner' step directly interpolates `inputs.*` and `steps.*.outputs.*` expressions inside a `run:` shell script. Affected expressions include: `${{ inputs.base-path }}`, `${{ inputs.head-path }}`, `${{ steps.context.outputs.owner }}`, `${{ steps.context.outputs.repo }}`, `${{ steps.context.outputs.pr }}`, `${{ steps.context.outputs.commit }}`, `${{ steps.context.outputs.repo-url }}`, `${{ steps.context.outputs.branch }}`, `${{ inputs.enable-dashboard }}`, `${{ inputs.project }}`. These are substituted into the shell before parsing, enabling command injection.

Locations:

- `scan/action.yml:148`

### script-injection (severity: high)

Rule (a): scan/action.yml 'Update PR status' step directly interpolates `steps.context.outputs.*` expressions inside a `run:` shell script. Affected expressions: `${{ steps.context.outputs.repo-url }}`, `${{ steps.context.outputs.pr }}`, `${{ steps.context.outputs.pr-status }}`. These are substituted into the shell before parsing, enabling command injection.

Locations:

- `scan/action.yml:183`

### github-env-injection (severity: high)

scan/action.yml 'Determine version' step writes `VERSION` (sourced from `inputs.version` via the `VERSION` env var) to `$GITHUB_OUTPUT` without sanitization (`printf '%s' ... | tr -d '\n\r'`). A newline in the input value could inject additional key=value pairs into the output file. Offending lines: `echo "version=${VERSION}" >> $GITHUB_OUTPUT` and `echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT`.

Locations:

- `scan/action.yml:68`
- `scan/action.yml:69`

### github-env-injection (severity: high)

scan/action.yml 'Derive context' step writes multiple variables derived from `inputs.*` and `github.*` expressions (OWNER, REPO, PR, COMMIT, REPO_URL, BRANCH, MODE, PR_STATUS) to `$GITHUB_OUTPUT` without sanitization. The values are directly assigned from untrusted expression interpolation (e.g. `OWNER="${{ inputs.github-owner }}"`), then echoed unsanitized. A newline in any input could inject additional key=value pairs into the output file. Offending lines: `echo "owner=${OWNER}" >> $GITHUB_OUTPUT`, `echo "repo=${REPO}" >> $GITHUB_OUTPUT`, `echo "pr=${PR}" >> $GITHUB_OUTPUT`, `echo "commit=${COMMIT}" >> $GITHUB_OUTPUT`, `echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT`, `echo "branch=${BRANCH}" >> $GITHUB_OUTPUT`, `echo "mode=${MODE}" >> $GITHUB_OUTPUT`, `echo "pr-status=${PR_STATUS}" >> $GITHUB_OUTPUT`.

Locations:

- `scan/action.yml:120`
- `scan/action.yml:121`
- `scan/action.yml:122`
- `scan/action.yml:123`
- `scan/action.yml:124`
- `scan/action.yml:125`
- `scan/action.yml:126`
- `scan/action.yml:127`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all 8 findings across action.yml and scan/action.yml:

1. **unpinned-uses**: Pinned all 3 `uses:` references in action.yml to full 40-char SHAs: actions/checkout@11d5960a326750d5838078e36cf38b85af677262 (v4) and infracost/actions/setup@e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 (v3).

2. **script-injection (action.yml)**: Moved github.token, github.event.pull_request.number, and inputs.behavior out of the run: block into an env: block as INFRACOST_GITHUB_TOKEN, INFRACOST_PR_NUMBER, INFRACOST_BEHAVIOR.

3. **script-injection (scan/action.yml - Derive context)**: Moved all 14 inputs.* and github.* expressions into an env: block; shell script uses plain $VAR references.

4. **script-injection (scan/action.yml - Run scanner)**: Moved all inputs.* and steps.context.outputs.* expressions into an env: block; shell script uses plain $VAR references.

5. **script-injection (scan/action.yml - Update PR status)**: Moved all steps.context.outputs.* expressions into an env: block; shell script uses plain $VAR references.

6. **github-env-injection (Determine version)**: Added `printf '%s' "$VERSION" | tr -d '\n\r'` sanitization before writing version and tag to $GITHUB_OUTPUT.

7. **github-env-injection (Derive context)**: Added `printf '%s' "$VAR" | tr -d '\n\r'` sanitization for all 8 output variables (owner, repo, pr, commit, repo-url, branch, mode, pr-status) before writing to $GITHUB_OUTPUT.

8. **static-inline-injection**: Same fix as script-injection for action.yml - inputs.behavior moved to env: block.

### Iteration 2

**Fixes applied:** script-injection, hardcoded-credentials, unpinned-uses, missing-permissions

**Notes:**

Fixed all 8 findings across 6 workflow files:

1. scanner_release.yml - script-injection: Moved github.event_name, github.event.inputs.version, github.repository, and needs.version.outputs.version (in --title) from run: blocks into env: blocks. Also pinned actions/checkout@v4 and actions/setup-go@v6 to full SHAs.

2. examples_test.yml - script-injection: Moved github.token from run: block into env: block (GITHUB_TOKEN). Pinned all 3 actions/checkout@v4 references to full SHA.

3. setup_test.yml - script-injection: Moved matrix.version and secrets.GITHUB_TOKEN from run: block into env: block. hardcoded-credentials: Replaced hardcoded 'abcdefg123456' api-key with ${{ secrets.INFRACOST_API_KEY }} and updated Verify step accordingly. missing-permissions: Added top-level permissions: {} and job-level permissions: contents: read. Pinned actions/checkout@v4 to full SHA.

4. scanner_test.yml - missing-permissions: Added top-level permissions: {} and job-level permissions: contents: read to all 4 jobs. Pinned all actions (checkout, setup-go, cache, golangci-lint-action) to full SHAs.

5. codeql-analysis.yml - unpinned-uses: Pinned actions/checkout@v4, github/codeql-action/init@v3, and github/codeql-action/analyze@v3 to full SHAs.

6. verify_dist.yml - missing-permissions: Added top-level permissions: {} and job-level permissions: contents: read. Pinned actions/checkout@v4 to full SHA.

### Iteration 3

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection finding in `.github/workflows/scanner_release.yml` at line 32. The unsanitized `RAW_VERSION` (derived from user-controlled `github.event.inputs.version`) was being written directly to `$GITHUB_OUTPUT`. The fix introduces a `SAFE_VERSION` variable that strips newlines and carriage returns using `printf '%s' "${RAW_VERSION#v}" | tr -d '\n\r'` before writing to `$GITHUB_OUTPUT`, preventing newline injection attacks that could poison the output with arbitrary key-value pairs.

