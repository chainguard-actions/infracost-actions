<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/scanner/v0.2.7** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Determine version' step, the env var VERSION (sourced from inputs.version, which is user-controlled) is written to $GITHUB_OUTPUT via the write_output helper function using `printf '%s\n' "$value"`. Although a random heredoc delimiter is used to prevent key injection, the value itself is never sanitized with `printf '%s' ... | tr -d '\n\r'` before the write. A caller can supply a newline-containing version string to inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `action.yml:50`

### github-env-injection (severity: high)

In the 'Derive context' step, the env var INPUT_REPO_URL (sourced from inputs.repo-url, which is user-controlled) is assigned to REPO_URL and then written to $GITHUB_OUTPUT via the write_output helper function using `printf '%s\n' "$value"`. The value is never sanitized with `printf '%s' ... | tr -d '\n\r'` before the write. A caller can supply a newline-containing repo-url to inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `action.yml:92`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed both github-env-injection findings in action.yml:
1. 'Determine version' step (line ~50): Updated write_output helper to sanitize the value with `safe_value="$(printf '%s' "$value" | tr -d '\n\r')"` before writing to $GITHUB_OUTPUT, preventing newline injection via inputs.version.
2. 'Derive context' step (line ~92): Same fix applied to the write_output helper in that step, preventing newline injection via inputs.repo-url.
Both helpers now strip \n and \r from the value before the heredoc write, while preserving the random delimiter approach for key-name safety.

