<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--diff/scanner/v0.2.8

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--diff/scanner/v0.2.8** was hardened automatically. 0 finding(s) were identified and resolved across 1 iteration(s).

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the write_output helper function in both the 'Determine version' step (line 48) and the 'Derive context' step (line 103) of action.yml. In each function, added a sanitization step that strips newlines and carriage returns from the value before writing it to $GITHUB_OUTPUT: `local safe; safe=$(printf '%s' "$value" | tr -d '\n\r')`. The sanitized `$safe` variable is then used in the printf call that writes the value, preventing embedded newlines from injecting additional key=value pairs into GITHUB_OUTPUT.

