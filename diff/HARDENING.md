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

Fixed both github-env-injection findings in action.yml by replacing the heredoc-delimiter write_output helper with a sanitized version. In both the 'Determine version' step (line 47) and the 'Derive context' step (line 95), the write_output function now uses `printf '%s' "$value" | tr -d '\n\r'` to strip newlines/carriage returns from values before writing them to $GITHUB_OUTPUT using the simple `name=value` format. This prevents attacker-controlled inputs (inputs.version, inputs.github-owner, inputs.github-repo, inputs.pr-number, inputs.repo-url, inputs.pr-status, github.event.pull_request.title, github.event.pull_request.user.login, etc.) containing newline characters from injecting additional key=value pairs into $GITHUB_OUTPUT.

