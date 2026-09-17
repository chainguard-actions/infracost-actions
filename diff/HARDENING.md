<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--diff/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--diff/scanner/v0.2.7** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The 'Determine version' step defines a write_output() helper that writes values to $GITHUB_OUTPUT using `printf '%s\n' "$value"` without first sanitizing with `printf '%s' ... | tr -d '\n\r'`. The value written ($VERSION) is derived from the untrusted input `inputs.version`. Although a random heredoc delimiter is used to prevent delimiter-injection, the value itself is not stripped of newlines, allowing a crafted multi-line input to inject additional key=value pairs into $GITHUB_OUTPUT.

Locations:

- `action.yml:50`

### github-env-injection (severity: high)

The 'Derive context' step defines the same write_output() helper and uses it to write multiple untrusted values to $GITHUB_OUTPUT without `tr -d '\n\r'` sanitization. Values written include: $OWNER/$REPO/$PR/$REPO_URL/$PR_STATUS (from inputs.github-owner, inputs.github-repo, inputs.pr-number, inputs.repo-url, inputs.pr-status) and $PR_TITLE/$PR_AUTHOR/$PR_LABELS (from github.event.pull_request.title, .user.login, .labels.*.name). A PR title or other attacker-controlled field containing newlines could inject additional output entries. The required sanitization step (`safe=$(printf '%s' "$value" | tr -d '\n\r')`) is absent before every write.

Locations:

- `action.yml:116`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed both instances of the github-env-injection vulnerability in action.yml. In both the 'Determine version' step (line ~50) and the 'Derive context' step (line ~116), the write_output() helper function was updated to sanitize values before writing to $GITHUB_OUTPUT. Added `local safe_value` and `safe_value="$(printf '%s' "$value" | tr -d '\n\r')"` to strip newlines and carriage returns, then write `$safe_value` instead of `$value`. This prevents attacker-controlled values (e.g., PR titles, labels, or version inputs containing embedded newlines) from injecting additional key=value pairs into $GITHUB_OUTPUT.

