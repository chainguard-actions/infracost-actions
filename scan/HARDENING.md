<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/scanner/v0.2.7** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The 'Determine version' step writes the untrusted input `inputs.version` (via env var `VERSION`) to `$GITHUB_OUTPUT` using the `write_output` helper function, which calls `printf '%s\n' "$value"`. No `tr -d '\n\r'` sanitization is applied before the write. An attacker-controlled value containing newlines could inject additional key=value pairs into the GITHUB_OUTPUT file.

Locations:

- `action.yml:33`

### github-env-injection (severity: high)

The 'Derive context' step writes the untrusted input `inputs.repo-url` (via env var `INPUT_REPO_URL`) to `$GITHUB_OUTPUT` using the `write_output` helper function, which calls `printf '%s\n' "$value"`. No `tr -d '\n\r'` sanitization is applied before the write. An attacker-controlled value containing newlines could inject additional key=value pairs into the GITHUB_OUTPUT file.

Locations:

- `action.yml:79`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. 'Determine version' step (line 33): Updated write_output helper to sanitize the value with `value="$(printf '%s' "$2" | tr -d '\n\r')"` before writing to $GITHUB_OUTPUT, preventing newline injection via inputs.version.
2. 'Derive context' step (line 79): Same fix applied to the write_output helper in this step, preventing newline injection via inputs.repo-url.
Both fixes use printf '%s' to safely pass the value (avoiding interpretation of leading dashes) and tr -d '\n\r' to strip newlines and carriage returns before the value is written to $GITHUB_OUTPUT.

