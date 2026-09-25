<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.1** was hardened automatically. 7 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml references actions using mutable version tags instead of pinned full-length SHA commits. Failing references: `actions/checkout@v4` (lines 26 and 48) and `infracost/actions/setup@v3` (line 32). These should be pinned to a full 40-character commit SHA (e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`) to prevent supply-chain attacks.

Locations:

- `action.yml:26`
- `action.yml:32`
- `action.yml:48`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of ${{ }} inside run: shell command strings. In action.yml's 'Post Infracost comment' step, `${{github.token}}`, `${{github.event.pull_request.number}}`, and `${{inputs.behavior}}` are interpolated directly into the shell command. An attacker controlling a PR can inject arbitrary shell metacharacters via these values before the shell ever sees them.

Locations:

- `action.yml:65`
- `action.yml:66`
- `action.yml:67`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of ${{ }} inside run: shell command strings in diff/action.yml. The 'Derive context' step interpolates `${{ inputs.github-owner }}`, `${{ inputs.github-repo }}`, `${{ inputs.pr-number }}`, `${{ inputs.repo-url }}`, `${{ inputs.pr-status }}`, `${{ github.event.pull_request.number }}`, `${{ github.event.pull_request.title }}`, `${{ github.event.pull_request.user.login }}`, `${{ toJson(github.event.pull_request.labels.*.name) }}`, `${{ github.event_name }}`, `${{ github.event.action }}`, `${{ github.event.pull_request.merged }}`, and `${{ inputs.base-path }}`/`${{ inputs.head-path }}` directly into the shell script. The 'Run scanner' step interpolates `${{ inputs.base-path }}`, `${{ inputs.head-path }}`, `${{ steps.context.outputs.* }}`, and `${{ inputs.project }}` directly. The 'Update PR status' step interpolates `${{ steps.context.outputs.repo-url }}`, `${{ steps.context.outputs.pr }}`, and `${{ steps.context.outputs.pr-status }}` directly. All of these allow shell metacharacter injection.

Locations:

- `diff/action.yml:90`
- `diff/action.yml:91`
- `diff/action.yml:92`
- `diff/action.yml:93`
- `diff/action.yml:94`
- `diff/action.yml:99`
- `diff/action.yml:108`
- `diff/action.yml:109`
- `diff/action.yml:110`
- `diff/action.yml:114`
- `diff/action.yml:115`
- `diff/action.yml:117`
- `diff/action.yml:130`
- `diff/action.yml:155`
- `diff/action.yml:159`
- `diff/action.yml:160`
- `diff/action.yml:161`
- `diff/action.yml:162`
- `diff/action.yml:163`
- `diff/action.yml:164`
- `diff/action.yml:165`
- `diff/action.yml:166`
- `diff/action.yml:168`
- `diff/action.yml:178`
- `diff/action.yml:179`
- `diff/action.yml:180`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of ${{ }} inside run: shell command strings in scan/action.yml. The 'Derive context' step interpolates `${{ inputs.repo-url }}` directly into the shell script (line 73). The 'Run scanner' step interpolates `${{ inputs.path }}`, `${{ steps.context.outputs.repo-url }}`, and `${{ inputs.project }}` directly into the shell script. An attacker-controlled input value containing shell metacharacters will be executed by the shell.

Locations:

- `scan/action.yml:73`
- `scan/action.yml:84`
- `scan/action.yml:85`
- `scan/action.yml:89`

### github-env-injection (severity: high)

In diff/action.yml's 'Derive context' step, values derived from untrusted inputs (`inputs.github-owner`, `inputs.github-repo`, `inputs.pr-number`, `inputs.repo-url`, `inputs.pr-status`) and attacker-controllable GitHub context values (`github.event.pull_request.title`, `github.event.pull_request.user.login`, `github.event.pull_request.labels.*.name`, `github.event.pull_request.number`) are assigned to shell variables (OWNER, REPO, PR, REPO_URL, PR_STATUS, PR_TITLE, PR_AUTHOR, PR_LABELS) and then written directly to $GITHUB_OUTPUT without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). A newline in any of these values allows injection of arbitrary key=value pairs into GITHUB_OUTPUT, poisoning subsequent steps.

Locations:

- `diff/action.yml:138`
- `diff/action.yml:139`
- `diff/action.yml:140`
- `diff/action.yml:141`
- `diff/action.yml:142`
- `diff/action.yml:143`
- `diff/action.yml:144`
- `diff/action.yml:145`
- `diff/action.yml:146`
- `diff/action.yml:147`

### github-env-injection (severity: high)

In scan/action.yml's 'Derive context' step, the value of `inputs.repo-url` is assigned to the shell variable REPO_URL and then written directly to $GITHUB_OUTPUT (`echo "repo-url=${REPO_URL}" >> $GITHUB_OUTPUT`) without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). A newline embedded in the caller-supplied `repo-url` input allows injection of arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `scan/action.yml:79`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all 7 findings across 3 files:

1. action.yml - unpinned-uses: Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262 (both occurrences at lines 26 and 48) and infracost/actions/setup@v3 to SHA e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 (line 32).

2. action.yml - script-injection/static-inline-injection: Moved ${{github.token}}, ${{github.event.pull_request.number}}, and ${{inputs.behavior}} from the 'Post Infracost comment' run: shell string into an env: block as INFRACOST_GITHUB_TOKEN, INFRACOST_PR_NUMBER, and INFRACOST_BEHAVIOR.

3. diff/action.yml - script-injection: Moved all ${{ }} expressions in 'Derive context', 'Run scanner', and 'Update PR status' steps into env: blocks.

4. diff/action.yml - github-env-injection: Added printf '%s' ... | tr -d '\n\r' sanitization for all 10 values written to $GITHUB_OUTPUT in 'Derive context', and quoted $GITHUB_OUTPUT references.

5. scan/action.yml - script-injection: Moved inputs.repo-url, inputs.path, steps.context.outputs.repo-url, and inputs.project into env: blocks in 'Derive context' and 'Run scanner' steps.

6. scan/action.yml - github-env-injection: Added printf '%s' ... | tr -d '\n\r' sanitization before writing repo-url to $GITHUB_OUTPUT, and quoted $GITHUB_OUTPUT reference.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in both diff/action.yml (lines 59-60) and scan/action.yml (lines 44-45). In the 'Determine version' step of each file, added sanitization: `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` before writing to $GITHUB_OUTPUT, and replaced `${VERSION}` with `${safe_version}` in both echo statements. This prevents newline injection attacks via the untrusted inputs.version caller-controlled input.

