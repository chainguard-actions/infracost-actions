<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--diff/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--diff/scanner/v0.2.7** was hardened automatically. 0 finding(s) were identified and resolved across 1 iteration(s).

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the unsanitized write_output helper function in both the 'Determine version' step and the 'Derive context' step. In each step, added `safe_value="$(printf '%s' "$value" | tr -d '\n\r')"` to strip embedded newlines and carriage returns from the value before writing it to $GITHUB_OUTPUT. The sanitized `$safe_value` variable is now used in place of the raw `$value` in the printf call that writes to $GITHUB_OUTPUT, preventing newline injection attacks from attacker-controlled inputs.

