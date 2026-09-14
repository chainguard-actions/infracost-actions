<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/v3.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml contains three `uses:` references pinned to mutable version tags (@v3) instead of immutable full 40-character commit SHAs. This exposes the action to supply-chain attacks if the referenced tag is moved or the upstream repository is compromised. Affected references: `actions/checkout@v3` (appears twice) and `infracost/actions/setup@v3`.

Locations:

- `action.yml:25`
- `action.yml:31`
- `action.yml:44`

### script-injection (severity: high)

Sub-rule (a) violation: The 'Post Infracost comment' run: block in action.yml directly interpolates GitHub Actions expressions inside shell command strings without routing through env: variables. The offending lines are:
  `--github-token=${{github.token}}` — injects the github context directly into the shell command
  `--pull-request=${{github.event.pull_request.number}}` — injects a pull-request-controlled value directly into the shell command
  `--behavior=${{inputs.behavior}}` — injects a caller-controlled input directly into the shell command
Any of these values could contain shell metacharacters that would be interpreted by the shell before the CLI tool receives them. All three expressions must be moved to an env: block and the env vars must be double-quoted in the run: script.

Locations:

- `action.yml:66`
- `action.yml:67`
- `action.yml:68`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all three findings in action.yml: (1) Pinned actions/checkout@v3 (appears twice) to full SHA a37ce9120846195fa4ece8f58b268e6043cb2f26 with # v3 comment. (2) Pinned infracost/actions/setup@v3 to full SHA e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 with # v3 comment. (3) Moved all three ${{...}} expressions (github.token, github.event.pull_request.number, inputs.behavior) from the run: shell string into an env: block as GITHUB_TOKEN, PULL_REQUEST_NUMBER, and INFRACOST_BEHAVIOR, then referenced them as double-quoted env vars in the shell script.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted `$GITHUB_REPOSITORY` in the 'Post Infracost comment' step of action.yml. Changed `--repo=$GITHUB_REPOSITORY` to `--repo="$GITHUB_REPOSITORY"` to prevent shell metacharacter injection. This makes it consistent with the other already-quoted arguments in the same infracost command.

