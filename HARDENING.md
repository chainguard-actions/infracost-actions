<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three actions using mutable tag refs instead of pinned 40-character commit SHAs. Unpinned tags can be silently moved to point to different (potentially malicious) commits. Failing references: `actions/checkout@v4` (used twice) and `infracost/actions/setup@v3`.

Locations:

- `action.yml:24`
- `action.yml:32`
- `action.yml:44`

### script-injection (severity: high)

Sub-rule (a): Multiple ${{ ... }} expressions are interpolated directly inside run: shell command strings, allowing an attacker to inject arbitrary shell commands via pull request data or action inputs. In action.yml 'Post Infracost comment' step: `${{github.token}}`, `${{github.event.pull_request.number}}`, and `${{inputs.behavior}}` are embedded directly in the shell command. In scan/action.yml 'Derive context' step: `${{ inputs.github-owner }}`, `${{ inputs.github-repo }}`, `${{ inputs.pr-number }}`, `${{ inputs.commit-sha }}`, `${{ inputs.repo-url }}`, `${{ inputs.branch }}`, `${{ inputs.pr-status }}`, `${{ github.event.pull_request.number }}`, `${{ github.event.pull_request.base.ref }}`, `${{ github.event_name }}`, `${{ github.event.action }}`, `${{ github.event.pull_request.merged }}`, `${{ inputs.base-path }}`, and `${{ inputs.head-path }}` are all interpolated directly into the shell script. In scan/action.yml 'Run scanner' step: `${{ inputs.base-path }}`, `${{ inputs.head-path }}`, `${{ steps.context.outputs.* }}`, `${{ inputs.enable-dashboard }}`, and `${{ inputs.project }}` are interpolated directly. In scan/action.yml 'Update PR status' step: `${{ steps.context.outputs.repo-url }}`, `${{ steps.context.outputs.pr }}`, and `${{ steps.context.outputs.pr-status }}` are interpolated directly. All these values should be passed via env: variables and then referenced as quoted shell variables.

Locations:

- `action.yml:57`
- `scan/action.yml:75`
- `scan/action.yml:130`
- `scan/action.yml:175`

### github-env-injection (severity: high)

Untrusted input values are written to $GITHUB_OUTPUT and $GITHUB_PATH without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). In scan/action.yml 'Determine version' step: `VERSION` (sourced from `inputs.version` via the `VERSION` env var) is written directly to $GITHUB_OUTPUT with `echo "version=${VERSION}" >> $GITHUB_OUTPUT` and `echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT` — a newline in the input value could inject additional key=value pairs. In scan/action.yml 'Derive context' step: `OWNER`, `REPO`, `PR`, `COMMIT`, `REPO_URL`, `BRANCH`, `MODE`, and `PR_STATUS` — all derived from `inputs.*` and `github.*` expressions — are written to $GITHUB_OUTPUT via `echo "key=${VAR}" >> $GITHUB_OUTPUT` without sanitization.

Locations:

- `scan/action.yml:64`
- `scan/action.yml:65`
- `scan/action.yml:118`
- `scan/action.yml:119`
- `scan/action.yml:120`
- `scan/action.yml:121`
- `scan/action.yml:122`
- `scan/action.yml:123`
- `scan/action.yml:124`
- `scan/action.yml:125`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all four findings:
1. unpinned-uses: Pinned actions/checkout@v4 (×2) to SHA 11d5960a326750d5838078e36cf38b85af677262 and infracost/actions/setup@v3 to SHA e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 in action.yml.
2. script-injection: Moved all ${{ }} expressions from run: blocks into env: maps in action.yml (Post Infracost comment step) and scan/action.yml (Derive context, Run scanner, Update PR status steps). Shell scripts now reference only plain environment variables.
3. github-env-injection: Added printf '%s' ... | tr -d '\n\r' sanitization for all values written to $GITHUB_OUTPUT in scan/action.yml Determine version step (safe_version) and Derive context step (safe_owner, safe_repo, safe_pr, safe_commit, safe_repo_url, safe_branch, safe_mode, safe_pr_status).
4. static-inline-injection: Covered by the script-injection fix — inputs.behavior is now in the BEHAVIOR env var.

