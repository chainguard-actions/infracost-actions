<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/scanner/v0.2.7** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Determine version' step, the value of $VERSION (sourced from inputs.version via the VERSION env var) is written to $GITHUB_OUTPUT through the write_output helper function without the required sanitization step (`printf '%s' "$VERSION" | tr -d '\n\r'`). An attacker-controlled input containing newline characters could inject arbitrary key=value pairs into $GITHUB_OUTPUT, potentially poisoning subsequent steps. The write_output function uses a heredoc delimiter pattern which prevents delimiter collision but does NOT strip embedded newlines from the value itself.

Locations:

- `action.yml:57`

### github-env-injection (severity: high)

In the 'Derive context' step, the value of $REPO_URL (sourced from inputs.repo-url via the INPUT_REPO_URL env var) is written to $GITHUB_OUTPUT through the write_output helper function without the required sanitization step (`printf '%s' "$REPO_URL" | tr -d '\n\r'`). An attacker-controlled repo-url input containing newline characters could inject arbitrary key=value pairs into $GITHUB_OUTPUT, potentially poisoning subsequent steps.

Locations:

- `action.yml:113`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in action.yml:
1. 'Determine version' step (line 57): Added `SAFE_VERSION="$(printf '%s' "$VERSION" | tr -d '\n\r')"` before the write_output calls, and updated both write_output calls to use $SAFE_VERSION instead of $VERSION.
2. 'Derive context' step (line 113): Added `SAFE_REPO_URL="$(printf '%s' "$REPO_URL" | tr -d '\n\r')"` before the write_output call, and updated the write_output call to use $SAFE_REPO_URL instead of $REPO_URL.
Both fixes strip embedded newline and carriage-return characters from attacker-controlled inputs before they are written to $GITHUB_OUTPUT, preventing newline injection attacks.

