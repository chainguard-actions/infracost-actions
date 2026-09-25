<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.1** was hardened automatically. 9 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple ${{ }} expressions are directly interpolated inside run: shell command strings in the 'Post Infracost comment' step. The values ${{github.token}}, ${{github.event.pull_request.number}}, and ${{inputs.behavior}} are substituted directly into the shell script before execution, allowing an attacker to inject arbitrary shell commands via a crafted PR or workflow input.

Locations:

- `action.yml:63`
- `action.yml:64`
- `action.yml:65`

### script-injection (severity: high)

Sub-rule (a): The 'Derive context' step in diff/action.yml directly interpolates ${{ inputs.github-owner }}, ${{ inputs.github-repo }}, ${{ inputs.pr-number }}, ${{ inputs.repo-url }}, ${{ inputs.pr-status }}, ${{ github.event.pull_request.number }}, ${{ github.event.pull_request.title }}, ${{ github.event.pull_request.user.login }}, ${{ toJson(github.event.pull_request.labels.*.name) }}, ${{ github.event_name }}, ${{ github.event.action }}, ${{ github.event.pull_request.merged }}, ${{ inputs.base-path }}, and ${{ inputs.head-path }} directly inside the run: shell script. Attacker-controlled values (PR title, author, labels, inputs) are substituted into the shell before execution, enabling command injection.

Locations:

- `diff/action.yml:88`
- `diff/action.yml:89`
- `diff/action.yml:90`
- `diff/action.yml:91`
- `diff/action.yml:92`
- `diff/action.yml:97`
- `diff/action.yml:106`
- `diff/action.yml:107`
- `diff/action.yml:108`

### script-injection (severity: high)

Sub-rule (a): The 'Run scanner' step in diff/action.yml directly interpolates ${{ inputs.base-path }}, ${{ inputs.head-path }}, ${{ steps.context.outputs.owner }}, ${{ steps.context.outputs.repo }}, ${{ steps.context.outputs.pr }}, ${{ steps.context.outputs.repo-url }}, ${{ steps.context.outputs.pr-title }}, ${{ steps.context.outputs.pr-author }}, ${{ steps.context.outputs.pr-labels }}, ${{ steps.context.outputs.pipeline-run-id }}, and ${{ inputs.project }} directly inside the run: shell script. These values flow from attacker-controlled PR metadata and inputs.

Locations:

- `diff/action.yml:158`
- `diff/action.yml:159`
- `diff/action.yml:160`
- `diff/action.yml:161`
- `diff/action.yml:162`
- `diff/action.yml:163`
- `diff/action.yml:164`
- `diff/action.yml:165`
- `diff/action.yml:166`
- `diff/action.yml:167`
- `diff/action.yml:170`

### script-injection (severity: high)

Sub-rule (a): The 'Update PR status' step in diff/action.yml directly interpolates ${{ steps.context.outputs.repo-url }}, ${{ steps.context.outputs.pr }}, and ${{ steps.context.outputs.pr-status }} directly inside the run: shell script. These step outputs originate from attacker-controlled inputs and github context values.

Locations:

- `diff/action.yml:177`
- `diff/action.yml:178`
- `diff/action.yml:179`

### script-injection (severity: high)

Sub-rule (a): The 'Derive context' step in scan/action.yml directly interpolates ${{ inputs.repo-url }} inside the run: shell script. The 'Run scanner' step directly interpolates ${{ inputs.path }}, ${{ steps.context.outputs.repo-url }}, and ${{ inputs.project }} inside the run: shell script. These are attacker-controllable values substituted before shell execution.

Locations:

- `scan/action.yml:64`
- `scan/action.yml:80`
- `scan/action.yml:81`
- `scan/action.yml:85`

### github-env-injection (severity: high)

The 'Derive context' step in diff/action.yml assigns attacker-controlled values from github.event.pull_request.title, github.event.pull_request.user.login, github.event.pull_request.labels.*.name, and various inputs.* into shell variables (PR_TITLE, PR_AUTHOR, PR_LABELS, OWNER, REPO, PR, REPO_URL, PR_STATUS), then writes them to $GITHUB_OUTPUT without the required sanitization step (printf '%s' ... | tr -d '\n\r'). A newline in any of these values can inject arbitrary key=value pairs into GITHUB_OUTPUT, poisoning subsequent steps.

Locations:

- `diff/action.yml:131`
- `diff/action.yml:132`
- `diff/action.yml:133`
- `diff/action.yml:134`
- `diff/action.yml:135`
- `diff/action.yml:136`
- `diff/action.yml:137`
- `diff/action.yml:138`
- `diff/action.yml:139`
- `diff/action.yml:140`

### github-env-injection (severity: high)

The 'Derive context' step in scan/action.yml assigns the value of ${{ inputs.repo-url }} (an attacker-controllable input) to the shell variable REPO_URL and writes it to $GITHUB_OUTPUT without the required sanitization step (printf '%s' ... | tr -d '\n\r'). A newline in the input value can inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `scan/action.yml:68`

### unpinned-uses (severity: high)

The root action.yml references three actions pinned to mutable tags rather than immutable 40-character SHA digests: 'actions/checkout@v4' (used twice) and 'infracost/actions/setup@v3'. Tag-pinned references can be silently redirected to malicious code if the upstream repository is compromised or the tag is moved.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:47`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all findings across action.yml, diff/action.yml, and scan/action.yml:

1. action.yml: Pinned actions/checkout@v4 (×2) to SHA 11d5960a326750d5838078e36cf38b85af677262 and infracost/actions/setup@v3 to SHA e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832. Moved github.token, github.event.pull_request.number, and inputs.behavior from the 'Post Infracost comment' run: block into the step's env: block.

2. diff/action.yml 'Derive context' step: Moved all 14 ${{ }} expressions (inputs.*, github.event.*, github.event_name, github.event.action) into env: block. Added printf/tr sanitization for all 10 values written to $GITHUB_OUTPUT to prevent newline injection.

3. diff/action.yml 'Run scanner' step: Moved all 11 ${{ }} expressions (inputs.base-path, inputs.head-path, inputs.project, steps.context.outputs.*) into env: block.

4. diff/action.yml 'Update PR status' step: Moved steps.context.outputs.repo-url, steps.context.outputs.pr, and steps.context.outputs.pr-status into env: block.

5. scan/action.yml 'Derive context' step: Moved inputs.repo-url into env: block and added printf/tr sanitization before writing to $GITHUB_OUTPUT.

6. scan/action.yml 'Run scanner' step: Moved inputs.path, inputs.project, and steps.context.outputs.repo-url into env: block.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in both scan/action.yml and diff/action.yml. In the 'Determine version' step of each file, added sanitization of the VERSION variable before writing to $GITHUB_OUTPUT: `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` and replaced the direct `echo "version=${VERSION}"` and `echo "tag=scanner/v${VERSION}"` lines with sanitized equivalents using `$safe_version`. Also updated the $GITHUB_OUTPUT references to use double-quoted `"$GITHUB_OUTPUT"` for consistency.

