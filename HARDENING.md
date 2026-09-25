<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.1.1** was hardened automatically. 7 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in action.yml are pinned to mutable version tags rather than full 40-character commit SHAs. This exposes the action to supply-chain attacks if the upstream tag is moved or overwritten. Failing references: `actions/checkout@v4` (line 26), `infracost/actions/setup@v3` (line 32), `actions/checkout@v4` (line 47).

Locations:

- `action.yml:26`
- `action.yml:32`
- `action.yml:47`

### script-injection (severity: high)

Sub-rule (a): The 'Post Infracost comment' run: block in action.yml directly interpolates GitHub Actions expressions inside the shell command string. `${{github.token}}`, `${{github.event.pull_request.number}}`, and `${{inputs.behavior}}` are expanded by the YAML template engine before the shell ever sees them, allowing an attacker-controlled value to inject arbitrary shell commands. Offending lines:
  --github-token=${{github.token}} \
  --pull-request=${{github.event.pull_request.number}} \
  --behavior=${{inputs.behavior}}

Locations:

- `action.yml:68`
- `action.yml:69`
- `action.yml:70`

### script-injection (severity: high)

Sub-rule (a): The 'Derive context' run: block in scan/action.yml directly interpolates multiple `${{ inputs.* }}` and `${{ github.* }}` expressions inside the shell command string. Values such as `${{ inputs.github-owner }}`, `${{ inputs.github-repo }}`, `${{ inputs.pr-number }}`, `${{ inputs.commit-sha }}`, `${{ inputs.repo-url }}`, `${{ inputs.branch }}`, and `${{ github.event.pull_request.number }}` / `${{ github.event.pull_request.head.ref }}` are expanded before the shell sees them, enabling shell command injection. Offending lines include:
  OWNER="${{ inputs.github-owner }}"
  REPO="${{ inputs.github-repo }}"
  PR="${{ inputs.pr-number }}"
  COMMIT="${{ inputs.commit-sha }}"
  REPO_URL="${{ inputs.repo-url }}"
  BRANCH="${{ inputs.branch }}"
  PR="${{ github.event.pull_request.number }}"
  BRANCH="${{ github.event.pull_request.head.ref }}"

Locations:

- `scan/action.yml:91`
- `scan/action.yml:92`
- `scan/action.yml:93`
- `scan/action.yml:94`
- `scan/action.yml:95`
- `scan/action.yml:96`
- `scan/action.yml:103`
- `scan/action.yml:113`

### script-injection (severity: high)

Sub-rule (a): The 'Run scanner' run: block in scan/action.yml directly interpolates `${{ inputs.* }}` and `${{ steps.*.outputs.* }}` expressions inside the shell command string. Values such as `${{ inputs.base-path }}`, `${{ inputs.head-path }}`, `${{ steps.context.outputs.owner }}`, `${{ steps.context.outputs.repo }}`, `${{ steps.context.outputs.pr }}`, `${{ steps.context.outputs.commit }}`, `${{ steps.context.outputs.repo-url }}`, `${{ steps.context.outputs.branch }}`, and `${{ inputs.project }}` are expanded before the shell sees them, enabling shell command injection. Offending lines:
  --base-path "${{ inputs.base-path }}"
  --head-path "${{ inputs.head-path }}"
  --github-owner "${{ steps.context.outputs.owner }}"
  --github-repo "${{ steps.context.outputs.repo }}"
  --pr-number "${{ steps.context.outputs.pr }}"
  --commit-sha "${{ steps.context.outputs.commit }}"
  --repo-url "${{ steps.context.outputs.repo-url }}"
  --branch "${{ steps.context.outputs.branch }}"
  if [ -n "${{ inputs.project }}" ]

Locations:

- `scan/action.yml:122`
- `scan/action.yml:123`
- `scan/action.yml:124`
- `scan/action.yml:125`
- `scan/action.yml:126`
- `scan/action.yml:127`
- `scan/action.yml:128`
- `scan/action.yml:129`
- `scan/action.yml:131`

### github-env-injection (severity: high)

The 'Determine version' step in scan/action.yml writes the `VERSION` variable (sourced from `inputs.version` via the `VERSION` env var) to `$GITHUB_OUTPUT` without sanitization. An attacker-controlled version string containing newlines could inject arbitrary key=value pairs into the output context. Offending lines:
  echo "version=${VERSION}" >> $GITHUB_OUTPUT
  echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT

Locations:

- `scan/action.yml:60`
- `scan/action.yml:61`

### github-env-injection (severity: high)

The 'Derive context' step in scan/action.yml writes multiple variables derived from untrusted inputs (`inputs.github-owner`, `inputs.github-repo`, `inputs.pr-number`, `inputs.commit-sha`, `inputs.repo-url`, `inputs.branch`, `github.event.pull_request.number`, `github.event.pull_request.head.ref`) to `$GITHUB_OUTPUT` without sanitization. An attacker-controlled value containing newlines could inject arbitrary key=value pairs into the output context. Offending lines:
  echo "owner=${OWNER}" >> $GITHUB_OUTPUT
  echo "repo=${REPO}" >> $GITHUB_OUTPUT
  echo "pr=${PR}" >> $GITHUB_OUTPUT
  echo "commit=${COMMIT}" >> $GITHUB_OUTPUT
  echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT
  echo "branch=${BRANCH}" >> $GITHUB_OUTPUT

Locations:

- `scan/action.yml:115`
- `scan/action.yml:116`
- `scan/action.yml:117`
- `scan/action.yml:118`
- `scan/action.yml:119`
- `scan/action.yml:120`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all 7 findings across action.yml and scan/action.yml:

1. Pinned 3 unpinned `uses:` references in action.yml to full commit SHAs: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262, infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832.

2. Fixed script injection in action.yml 'Post Infracost comment' step: moved github.token, github.event.pull_request.number, and inputs.behavior to env: block as INFRACOST_GITHUB_TOKEN, INFRACOST_PR_NUMBER, INFRACOST_BEHAVIOR.

3. Fixed script injection in scan/action.yml 'Derive context' step: moved all inputs.* and github.event.* expressions to env: block (INPUT_GITHUB_OWNER, INPUT_GITHUB_REPO, INPUT_PR_NUMBER, INPUT_COMMIT_SHA, INPUT_REPO_URL, INPUT_BRANCH, EVENT_PR_NUMBER, EVENT_PR_HEAD_REF).

4. Fixed script injection in scan/action.yml 'Run scanner' step: moved all inputs.* and steps.context.outputs.* expressions to env: block (INPUT_BASE_PATH, INPUT_HEAD_PATH, CONTEXT_OWNER, CONTEXT_REPO, CONTEXT_PR, CONTEXT_COMMIT, CONTEXT_REPO_URL, CONTEXT_BRANCH, INPUT_PROJECT).

5. Fixed github-env-injection in scan/action.yml 'Determine version' step: sanitize VERSION with tr -d '\n\r' before writing to GITHUB_OUTPUT.

6. Fixed github-env-injection in scan/action.yml 'Derive context' step: sanitize all 6 output values (owner, repo, pr, commit, repo-url, branch) with tr -d '\n\r' before writing to GITHUB_OUTPUT.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted shell variable expansion in action.yml line 70. Changed `--repo=$GITHUB_REPOSITORY` to `--repo="$GITHUB_REPOSITORY"` in the 'Post Infracost comment' step's run block. This prevents shell metacharacter injection via the GITHUB_REPOSITORY environment variable, which a malicious caller could override in a composite action context.

