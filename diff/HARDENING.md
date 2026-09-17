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

Fixed both write_output() helper functions in action.yml to sanitize values before writing to $GITHUB_OUTPUT. In both the 'Determine version' step (lines ~60, 71) and the 'Derive context' step (lines ~120, 155), added a `safe_value` local variable that strips embedded newlines and carriage returns using `printf '%s' "$value" | tr -d '\n\r'`. The sanitized value is then used in the printf that writes to $GITHUB_OUTPUT, preventing injection of arbitrary key=value pairs via attacker-controllable inputs (inputs.version, inputs.github-owner, inputs.github-repo, inputs.pr-number, inputs.repo-url, inputs.pr-status, inputs.base-path, inputs.head-path) and github context values (PR title, author, labels, event name, event action, merged status).

