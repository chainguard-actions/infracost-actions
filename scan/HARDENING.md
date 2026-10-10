<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/scanner/v0.2.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/scanner/v0.2.9** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The 'Determine version' step writes the untrusted input value $VERSION (sourced from inputs.version via the VERSION env var) to $GITHUB_OUTPUT using `printf '%s\n' "$value" >> "$GITHUB_OUTPUT"` inside the write_output() function, without first sanitizing it with `printf '%s' ... | tr -d '\n\r'`. An attacker-controlled inputs.version value containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `action.yml:57`
- `action.yml:58`

### github-env-injection (severity: high)

The 'Derive context' step writes the untrusted input value $REPO_URL (sourced from inputs.repo-url via the INPUT_REPO_URL env var) to $GITHUB_OUTPUT using `printf '%s\n' "$value" >> "$GITHUB_OUTPUT"` inside the write_output() function, without first sanitizing it with `printf '%s' ... | tr -d '\n\r'`. An attacker-controlled inputs.repo-url value containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `action.yml:112`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in action.yml:
1. 'Determine version' step (lines 57-58): Added `safe_value="$(printf '%s' "$value" | tr -d '\n\r')"` in the write_output() function and changed the write to use `$safe_value` instead of `$value`. This sanitizes the VERSION input before writing to $GITHUB_OUTPUT.
2. 'Derive context' step (line 112): Applied the same fix to the write_output() function in that step, sanitizing the REPO_URL (from inputs.repo-url) before writing to $GITHUB_OUTPUT.
Both fixes follow the recommended pattern of sanitizing at the point of writing, using a separate variable to capture the sanitized value without nesting command substitutions.

