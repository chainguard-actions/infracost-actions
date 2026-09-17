<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/scanner/v0.2.7** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The 'Determine version' step writes the value of VERSION (sourced from inputs.version via the VERSION env var) to $GITHUB_OUTPUT using the write_output() helper. The helper uses a random heredoc delimiter to prevent key injection, but it does NOT sanitize the value with `printf '%s' ... | tr -d '\n\r'` before writing. An attacker-controlled input.version containing embedded newlines could inject additional key=value pairs into $GITHUB_OUTPUT, potentially poisoning downstream steps.

Offending pattern:
  write_output "version" "$VERSION"   # VERSION = inputs.version, no tr -d newlines
  write_output "tag" "scanner/v${VERSION}"

Locations:

- `action.yml:49`

### github-env-injection (severity: high)

The 'Derive context' step writes the value of REPO_URL (sourced from inputs.repo-url via the INPUT_REPO_URL env var) to $GITHUB_OUTPUT using the write_output() helper. The helper uses a random heredoc delimiter to prevent key injection, but it does NOT sanitize the value with `printf '%s' ... | tr -d '\n\r'` before writing. An attacker-controlled inputs.repo-url containing embedded newlines could inject additional key=value pairs into $GITHUB_OUTPUT, potentially poisoning downstream steps.

Offending pattern:
  write_output "repo-url" "$REPO_URL"   # REPO_URL = inputs.repo-url, no tr -d newlines

Locations:

- `action.yml:91`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in action.yml:

1. 'Determine version' step (line 49): Added `safe_value="$(printf '%s' "$value" | tr -d '\n\r')"` inside the write_output() helper and replaced `printf '%s\n' "$value"` with `printf '%s\n' "$safe_value"`. This sanitizes inputs.version before writing 'version' and 'tag' outputs to $GITHUB_OUTPUT.

2. 'Derive context' step (line 91): Applied the same fix to the write_output() helper in that step, sanitizing inputs.repo-url before writing the 'repo-url' output to $GITHUB_OUTPUT.

Both fixes strip embedded newlines and carriage returns from user-controlled values before they are written to $GITHUB_OUTPUT, preventing newline injection attacks that could poison downstream step outputs.

