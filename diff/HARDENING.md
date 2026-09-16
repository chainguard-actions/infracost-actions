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

Fixed both write_output() helper functions in action.yml (in the 'Determine version' step at line ~57 and the 'Derive context' step at line ~100). Each function now sanitizes the value before writing to $GITHUB_OUTPUT: added `local safe` and `safe=$(printf '%s' "$value" | tr -d '\n\r')`, then replaced `printf '%s\n' "$value"` with `printf '%s\n' "$safe"` inside the heredoc block. This prevents newline injection from attacker-controllable inputs like $VERSION, $PR_TITLE, $PR_AUTHOR, $PR_LABELS, $OWNER, $REPO, $PR, $REPO_URL, and $PR_STATUS.

