<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three external actions using mutable version tags instead of pinned full-length SHA digests. An attacker who compromises the upstream repository could push malicious code under the same tag. Failing references: `uses: actions/checkout@v4` (appears twice) and `uses: infracost/actions/setup@v3`.

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:46`

### github-env-injection (severity: high)

diff/action.yml — 'Determine version' step: the env var `VERSION` is sourced from `inputs.version` (caller-controlled) and written to `$GITHUB_OUTPUT` via `write_output "version" "$VERSION"` without the required `printf '%s' ... | tr -d '\n\r'` sanitization. Although a random heredoc delimiter is used, the check requires explicit newline stripping before every write of untrusted input to a special environment file.

Locations:

- `diff/action.yml:70`

### github-env-injection (severity: high)

diff/action.yml — 'Derive context' step: multiple values derived from attacker-controllable sources are written to `$GITHUB_OUTPUT` via `write_output` without `tr -d '\n\r'` sanitization. Affected writes include: `write_output "owner"` ($INPUT_GITHUB_OWNER from `inputs.github-owner`), `write_output "repo"` ($INPUT_GITHUB_REPO from `inputs.github-repo`), `write_output "pr"` ($INPUT_PR_NUMBER / $EVENT_PR_NUMBER from `inputs.pr-number` / `github.event.pull_request.number`), `write_output "repo-url"` ($INPUT_REPO_URL from `inputs.repo-url`), `write_output "pr-title"` ($EVENT_PR_TITLE from `github.event.pull_request.title`), `write_output "pr-author"` ($EVENT_PR_AUTHOR from `github.event.pull_request.user.login`), `write_output "pr-labels"` (derived from `github.event.pull_request.labels.*.name`), and `write_output "pr-status"` ($INPUT_PR_STATUS from `inputs.pr-status`). All of these are untrusted inputs that must be sanitized before being written to `$GITHUB_OUTPUT`.

Locations:

- `diff/action.yml:145`

### github-env-injection (severity: high)

scan/action.yml — 'Determine version' step: the env var `VERSION` is sourced from `inputs.version` (caller-controlled) and written to `$GITHUB_OUTPUT` via `write_output "version" "$VERSION"` without the required `printf '%s' ... | tr -d '\n\r'` sanitization.

Locations:

- `scan/action.yml:55`

### github-env-injection (severity: high)

scan/action.yml — 'Derive context' step: `$INPUT_REPO_URL` (sourced from `inputs.repo-url`, caller-controlled) is written to `$GITHUB_OUTPUT` via `write_output "repo-url" "$REPO_URL"` without the required `printf '%s' ... | tr -d '\n\r'` sanitization.

Locations:

- `scan/action.yml:100`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed all 5 findings: (1) Pinned actions/checkout@v4 to SHA 11d5960a326750d5838078e36cf38b85af677262 (appears twice in action.yml) and infracost/actions/setup@v3 to SHA e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832. (2) In diff/action.yml 'Determine version' step: sanitized VERSION with `printf '%s' ... | tr -d '\n\r'` before writing to GITHUB_OUTPUT. (3) In diff/action.yml 'Derive context' step: sanitized all 8 untrusted values (owner, repo, pr, repo-url, pr-title, pr-author, pr-labels, pr-status) before writing to GITHUB_OUTPUT. (4) In scan/action.yml 'Determine version' step: sanitized VERSION before writing to GITHUB_OUTPUT. (5) In scan/action.yml 'Derive context' step: sanitized REPO_URL before writing to GITHUB_OUTPUT.

