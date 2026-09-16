<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/scanner/v0.2.7** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Determine version' step, the env var VERSION is sourced from the user-controlled input `${{ inputs.version }}` and written to $GITHUB_OUTPUT via the `write_output` helper function. The helper uses `printf '%s\n' "$value"` to write the value, but does NOT apply the required `tr -d '\n\r'` sanitization before the write. An attacker can supply a newline character in `inputs.version` to inject additional key=value pairs into $GITHUB_OUTPUT, potentially overwriting outputs consumed by downstream steps.

Locations:

- `action.yml:55`

### github-env-injection (severity: high)

In the 'Derive context' step, the env var INPUT_REPO_URL is sourced from the user-controlled input `${{ inputs.repo-url }}` and written to $GITHUB_OUTPUT via the `write_output` helper function. The helper uses `printf '%s\n' "$value"` to write the value, but does NOT apply the required `tr -d '\n\r'` sanitization before the write. An attacker can supply a newline character in `inputs.repo-url` to inject additional key=value pairs into $GITHUB_OUTPUT, potentially overwriting outputs consumed by downstream steps.

Locations:

- `action.yml:100`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. 'Determine version' step (line 55): Added `safe_value="$(printf '%s' "$value" | tr -d '\n\r')"` in the write_output helper and replaced `$value` with `$safe_value` in the printf call.
2. 'Derive context' step (line 100): Same fix applied to the write_output helper in that step.
Both fixes prevent newline injection via user-controlled inputs (inputs.version and inputs.repo-url) into $GITHUB_OUTPUT by stripping all newline and carriage return characters before writing.

