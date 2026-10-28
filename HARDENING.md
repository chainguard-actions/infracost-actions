<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/v4.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/v4.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple ${{ ... }} expressions are interpolated directly inside run: shell command strings.

In action.yml (Post Infracost comment step, ~line 63):
  --github-token=${{github.token}} \
  --pull-request=${{github.event.pull_request.number}} \
  --behavior=${{inputs.behavior}}
These values are injected directly into the shell command before the shell ever sees them, allowing an attacker to inject arbitrary shell commands via inputs.behavior or github.event.pull_request.number.

In diff/action.yml (Derive context step, ~lines 84–121):
  OWNER="${{ inputs.github-owner }}"
  REPO="${{ inputs.github-repo }}"
  PR="${{ inputs.pr-number }}"
  REPO_URL="${{ inputs.repo-url }}"
  PR_STATUS="${{ inputs.pr-status }}"
  PR="${{ github.event.pull_request.number }}"
  PR_TITLE="${{ github.event.pull_request.title }}"
  PR_AUTHOR="${{ github.event.pull_request.user.login }}"
  PR_LABELS=$(echo '${{ toJson(github.event.pull_request.labels.*.name) }}' | jq -r 'join(",")')
  if [ "${{ github.event_name }}" = "pull_request" ]; then
  case "${{ github.event.action }}" in
  if [ "${{ github.event.pull_request.merged }}" = "true" ]; then
  if [ -z "${{ inputs.base-path }}" ] || [ -z "${{ inputs.head-path }}" ]; then

In diff/action.yml (Run scanner step, ~lines 138–150):
  if [ -z "${{ inputs.base-path }}" ] || [ -z "${{ inputs.head-path }}" ]; then
  --base-path "${{ inputs.base-path }}"
  --head-path "${{ inputs.head-path }}"
  --github-owner "${{ steps.context.outputs.owner }}"
  --github-repo "${{ steps.context.outputs.repo }}"
  --pr-number "${{ steps.context.outputs.pr }}"
  --repo-url "${{ steps.context.outputs.repo-url }}"
  --pr-title "${{ steps.context.outputs.pr-title }}"
  --pr-author "${{ steps.context.outputs.pr-author }}"
  --pr-labels "${{ steps.context.outputs.pr-labels }}"
  --pipeline-run-id "${{ steps.context.outputs.pipeline-run-id }}"
  if [ -n "${{ inputs.project }}" ]; then

In diff/action.yml (Update PR status step, ~lines 155–157):
  --repo-url "${{ steps.context.outputs.repo-url }}"
  --pr-number "${{ steps.context.outputs.pr }}"
  --status "${{ steps.context.outputs.pr-status }}"

In scan/action.yml (Derive context step, ~line 76):
  REPO_URL="${{ inputs.repo-url }}"

In scan/action.yml (Run scanner step, ~lines 83–87):
  --path "${{ inputs.path }}"
  if [ -n "${{ inputs.project }}" ]; then

Locations:

- `action.yml:63`
- `diff/action.yml:84`
- `diff/action.yml:138`
- `diff/action.yml:155`
- `scan/action.yml:76`
- `scan/action.yml:83`

### github-env-injection (severity: high)

Untrusted input values are written to $GITHUB_OUTPUT without the required sanitization step (printf '%s' ... | tr -d '\n\r').

In diff/action.yml (Determine version step): VERSION is sourced from inputs.version (via env var) and written unsanitized:
  echo "version=${VERSION}" >> $GITHUB_OUTPUT
  echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT

In diff/action.yml (Derive context step): PR_TITLE, PR_AUTHOR, PR_LABELS, OWNER, REPO, PR, REPO_URL, PR_STATUS are all derived from inputs.* and github.event.* and written unsanitized to $GITHUB_OUTPUT:
  echo "owner=${OWNER}" >> $GITHUB_OUTPUT
  echo "repo=${REPO}" >> $GITHUB_OUTPUT
  echo "pr=${PR}" >> $GITHUB_OUTPUT
  echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT
  echo "pr-title=${PR_TITLE}" >> $GITHUB_OUTPUT
  echo "pr-author=${PR_AUTHOR}" >> $GITHUB_OUTPUT
  echo "pr-labels=${PR_LABELS}" >> $GITHUB_OUTPUT
  echo "pr-status=${PR_STATUS}" >> $GITHUB_OUTPUT
A PR title or author name containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT.

In scan/action.yml (Determine version step): VERSION from inputs.version written unsanitized:
  echo "version=${VERSION}" >> $GITHUB_OUTPUT
  echo "tag=scanner/v${VERSION}" >> $GITHUB_OUTPUT

In scan/action.yml (Derive context step): REPO_URL from inputs.repo-url written unsanitized:
  echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT

Locations:

- `diff/action.yml:57`
- `diff/action.yml:126`
- `scan/action.yml:43`
- `scan/action.yml:80`

### unpinned-uses (severity: high)

The following uses: references in action.yml are pinned to mutable tags rather than full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved:
  - uses: actions/checkout@v4  (line 24, Checkout base branch step)
  - uses: infracost/actions/setup@v3  (line 30, Setup Infracost step)
  - uses: actions/checkout@v4  (line 46, anonymous checkout step)
All three should be pinned to a full SHA digest, e.g. actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4

Locations:

- `action.yml:24`
- `action.yml:30`
- `action.yml:46`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection, github-env-injection

**Notes:**

Fixed all four findings across three files:

1. action.yml - unpinned-uses: Pinned actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 # v4 (both occurrences) and infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3.

2. action.yml - script-injection/static-inline-injection: Moved ${{github.token}}, ${{github.event.pull_request.number}}, and ${{inputs.behavior}} from the 'Post Infracost comment' run: block into an env: block (INFRACOST_GITHUB_TOKEN, INFRACOST_PR_NUMBER, INFRACOST_BEHAVIOR). Shell script now references these as plain env vars.

3. diff/action.yml - script-injection: All ${{ }} expressions in 'Derive context', 'Run scanner', and 'Update PR status' steps moved to env: blocks. Shell scripts reference plain env vars.

4. diff/action.yml - github-env-injection: All values written to $GITHUB_OUTPUT in 'Determine version' and 'Derive context' steps are now sanitized with printf '%s' ... | tr -d '\n\r' before writing.

5. scan/action.yml - script-injection: All ${{ }} expressions in 'Derive context' and 'Run scanner' steps moved to env: blocks.

6. scan/action.yml - github-env-injection: VERSION and REPO_URL values written to $GITHUB_OUTPUT in 'Determine version' and 'Derive context' steps are now sanitized with printf '%s' ... | tr -d '\n\r'.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed the unquoted $GITHUB_REPOSITORY variable in the 'Post Infracost comment' step of action.yml (line 71). Changed `--repo=$GITHUB_REPOSITORY` to `--repo="$GITHUB_REPOSITORY"` to prevent shell word-splitting or command injection from metacharacters in the repository name.

