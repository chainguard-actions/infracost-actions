<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/v4.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **infracost--actions/v4.1.0** was hardened automatically. 10 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml contains three unpinned `uses:` references with mutable tag-based refs instead of full 40-character SHA commit hashes. This exposes the action to supply-chain attacks if the referenced tags are moved or compromised. Failing references: `actions/checkout@v4` (line 25), `infracost/actions/setup@v3` (line 32), `actions/checkout@v4` (line 49).

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:49`

### script-injection (severity: high)

Sub-rule (a): The 'Post Infracost comment' run block in action.yml directly interpolates GitHub Actions expressions inside shell commands without routing through env vars. The expressions `${{github.token}}`, `${{github.event.pull_request.number}}`, and `${{inputs.behavior}}` are substituted by the template engine before the shell parses the command, allowing an attacker to inject arbitrary shell metacharacters via a crafted PR or workflow input. Offending lines:
  `--github-token=${{github.token}}`
  `--pull-request=${{github.event.pull_request.number}}`
  `--behavior=${{inputs.behavior}}`

Locations:

- `action.yml:65`
- `action.yml:66`
- `action.yml:67`

### script-injection (severity: high)

Sub-rule (a): The 'Derive context' run block in diff/action.yml directly interpolates multiple GitHub Actions expressions inside shell commands. Attacker-controlled values such as `${{ inputs.github-owner }}`, `${{ inputs.github-repo }}`, `${{ inputs.pr-number }}`, `${{ inputs.repo-url }}`, `${{ inputs.pr-status }}`, `${{ github.event.pull_request.number }}`, `${{ github.event.pull_request.title }}`, `${{ github.event.pull_request.user.login }}`, `${{ toJson(github.event.pull_request.labels.*.name) }}`, `${{ github.event_name }}`, `${{ github.event.action }}`, `${{ github.event.pull_request.merged }}`, `${{ inputs.base-path }}`, and `${{ inputs.head-path }}` are all substituted by the template engine before the shell parses the script, enabling shell command injection.

Locations:

- `diff/action.yml:93`

### script-injection (severity: high)

Sub-rule (a): The 'Run scanner' run block in diff/action.yml directly interpolates GitHub Actions expressions inside shell commands. Values `${{ inputs.base-path }}`, `${{ inputs.head-path }}`, `${{ steps.context.outputs.owner }}`, `${{ steps.context.outputs.repo }}`, `${{ steps.context.outputs.pr }}`, `${{ steps.context.outputs.repo-url }}`, `${{ steps.context.outputs.pr-title }}`, `${{ steps.context.outputs.pr-author }}`, `${{ steps.context.outputs.pr-labels }}`, `${{ steps.context.outputs.pipeline-run-id }}`, and `${{ inputs.project }}` are all interpolated directly into the shell script before execution, enabling command injection.

Locations:

- `diff/action.yml:155`

### script-injection (severity: high)

Sub-rule (a): The 'Update PR status' run block in diff/action.yml directly interpolates `${{ steps.context.outputs.repo-url }}`, `${{ steps.context.outputs.pr }}`, and `${{ steps.context.outputs.pr-status }}` inside shell commands. These step outputs are derived from attacker-controlled inputs and github context values, enabling command injection.

Locations:

- `diff/action.yml:179`

### script-injection (severity: high)

Sub-rule (a): The 'Derive context' run block in scan/action.yml directly interpolates `${{ inputs.repo-url }}` inside a shell command. This value is caller-controlled and is substituted by the template engine before the shell parses the script, enabling command injection.

Locations:

- `scan/action.yml:73`

### script-injection (severity: high)

Sub-rule (a): The 'Run scanner' run block in scan/action.yml directly interpolates `${{ inputs.path }}`, `${{ steps.context.outputs.repo-url }}`, and `${{ inputs.project }}` inside shell commands. These are caller-controlled values substituted before shell parsing, enabling command injection.

Locations:

- `scan/action.yml:85`

### github-env-injection (severity: high)

The 'Derive context' run block in diff/action.yml writes attacker-controlled values to `$GITHUB_OUTPUT` without the required sanitization (`printf '%s' ... | tr -d '\n\r'`). Specifically: `PR_TITLE` (from `${{ github.event.pull_request.title }}`), `PR_AUTHOR` (from `${{ github.event.pull_request.user.login }}`), `PR_LABELS` (from `${{ toJson(github.event.pull_request.labels.*.name) }}`), `OWNER` (from `${{ inputs.github-owner }}`), `REPO` (from `${{ inputs.github-repo }}`), `PR` (from `${{ inputs.pr-number }}`), and `REPO_URL` (from `${{ inputs.repo-url }}`) are all written to `$GITHUB_OUTPUT` via unsanitized `echo` statements. A newline in any of these values can inject arbitrary key=value pairs into the output file, poisoning subsequent steps.

Locations:

- `diff/action.yml:137`

### github-env-injection (severity: high)

The 'Derive context' run block in scan/action.yml writes the caller-controlled value `${{ inputs.repo-url }}` (stored in `REPO_URL`) to `$GITHUB_OUTPUT` via `echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT` without the required sanitization (`printf '%s' ... | tr -d '\n\r'`). A newline embedded in the input can inject arbitrary key=value pairs into the output file.

Locations:

- `scan/action.yml:80`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all 10 findings across 3 files:

**action.yml**: Pinned actions/checkout@v4 to SHA 34e114876b0b11c390a56381ad16ebd13914f8d5 (×2) and infracost/actions/setup@v3 to SHA e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832. Moved github.token, github.event.pull_request.number, and inputs.behavior from the 'Post Infracost comment' run block into the step's env: block.

**diff/action.yml**: Moved all 14 GitHub expressions in 'Derive context' into env: block. Added printf/tr sanitization for all 10 values written to $GITHUB_OUTPUT. Moved all expressions in 'Run scanner' (base-path, head-path, context outputs, project) into env: block. Moved all expressions in 'Update PR status' (repo-url, pr, pr-status) into env: block.

**scan/action.yml**: Moved inputs.repo-url in 'Derive context' into env: block and added printf/tr sanitization before writing to $GITHUB_OUTPUT. Moved inputs.path, context.outputs.repo-url, and inputs.project in 'Run scanner' into env: block.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in both diff/action.yml and scan/action.yml. In the 'Determine version' step of each file, added sanitization of the VERSION variable before writing to $GITHUB_OUTPUT: `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` and then used `$safe_version` in both `echo "version=${safe_version}"` and `echo "tag=scanner/v${safe_version}"` output lines. This prevents newline injection attacks that could allow an attacker to inject arbitrary key=value pairs into GITHUB_OUTPUT.

### Iteration 3

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted shell variable expansion in action.yml line 63: changed `--repo=$GITHUB_REPOSITORY \` to `--repo="$GITHUB_REPOSITORY" \`. This ensures the GITHUB_REPOSITORY environment variable is properly double-quoted, preventing shell metacharacter interpretation.

