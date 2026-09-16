<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three external actions using mutable tag-based refs instead of full 40-character commit SHA pins. An attacker who compromises the upstream repository or tag could inject malicious code. Failing references: `actions/checkout@v4` (line 25), `infracost/actions/setup@v3` (line 32), `actions/checkout@v4` (line 44).

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:44`

### github-env-injection (severity: high)

diff/action.yml — 'Determine version' step: The write_output helper writes $VERSION (sourced from inputs.version via the VERSION env var) to $GITHUB_OUTPUT using `printf '%s\n' "$value"` without the required `tr -d '\n\r'` sanitization. A value containing newlines could inject additional key=value pairs into GITHUB_OUTPUT. Although a random heredoc delimiter is used (preventing delimiter-collision injection), the value itself is not stripped of newlines as required by the check.

Locations:

- `diff/action.yml:72`

### github-env-injection (severity: high)

diff/action.yml — 'Derive context' step: The write_output helper writes multiple untrusted values to $GITHUB_OUTPUT without `tr -d '\n\r'` sanitization. Affected values include: $OWNER (from inputs.github-owner), $REPO (from inputs.github-repo), $PR (from inputs.pr-number or github.event.pull_request.number), $REPO_URL (from inputs.repo-url), $PR_TITLE (from github.event.pull_request.title), $PR_AUTHOR (from github.event.pull_request.user.login), $PR_LABELS (from github.event.pull_request.labels.*.name), and $PR_STATUS (from inputs.pr-status). An attacker-controlled newline in any of these values could inject additional output variables.

Locations:

- `diff/action.yml:155`

### github-env-injection (severity: high)

scan/action.yml — 'Determine version' step: The write_output helper writes $VERSION (sourced from inputs.version via the VERSION env var) to $GITHUB_OUTPUT using `printf '%s\n' "$value"` without the required `tr -d '\n\r'` sanitization. A crafted version string containing newlines could inject additional key=value pairs into GITHUB_OUTPUT.

Locations:

- `scan/action.yml:58`

### github-env-injection (severity: high)

scan/action.yml — 'Derive context' step: The write_output helper writes $REPO_URL (sourced from inputs.repo-url via the INPUT_REPO_URL env var) to $GITHUB_OUTPUT using `printf '%s\n' "$value"` without the required `tr -d '\n\r'` sanitization. A crafted repo-url value containing newlines could inject additional output variables.

Locations:

- `scan/action.yml:107`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed 3 unpinned action references in action.yml by pinning to full commit SHAs (actions/checkout@v4 → SHA 11d5960a..., infracost/actions/setup@v3 → SHA e9d6e6cd...). Fixed 4 github-env-injection vulnerabilities by updating the write_output() helper function in diff/action.yml ('Determine version' and 'Derive context' steps) and scan/action.yml ('Determine version' and 'Derive context' steps) to sanitize values with `safe_value=$(printf '%s' "$value" | tr -d '\n\r')` before writing to $GITHUB_OUTPUT, preventing newline injection attacks.

