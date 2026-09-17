<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--diff/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--diff/scanner/v0.2.7** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The 'Determine version' step writes the untrusted input value `$VERSION` (sourced from `inputs.version` via the `VERSION` env var) to `$GITHUB_OUTPUT` using `printf '%s\n' "$value"` inside the `write_output` helper — without the required `printf '%s' ... | tr -d '\n\r'` sanitization. A newline embedded in the version input could inject additional key=value pairs into GITHUB_OUTPUT, allowing an attacker to set arbitrary step outputs.

Locations:

- `action.yml:68`

### github-env-injection (severity: high)

The 'Derive context' step writes multiple untrusted values to `$GITHUB_OUTPUT` via the same unsanitized `write_output` helper (which uses `printf '%s\n' "$value"` without `tr -d '\n\r'`). The values written include: `$OWNER` and `$REPO` (from `inputs.github-owner`/`inputs.github-repo`), `$PR` (from `inputs.pr-number` or `github.event.pull_request.number`), `$REPO_URL` (from `inputs.repo-url`), `$PR_TITLE` (from `github.event.pull_request.title`), `$PR_AUTHOR` (from `github.event.pull_request.user.login`), `$PR_LABELS` (from `github.event.pull_request.labels.*.name`), and `$PR_STATUS` (from `inputs.pr-status`). Newlines in any of these attacker-controlled values could inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `action.yml:152`
- `action.yml:153`
- `action.yml:154`
- `action.yml:155`
- `action.yml:156`
- `action.yml:157`
- `action.yml:158`
- `action.yml:159`
- `action.yml:160`
- `action.yml:161`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed both write_output helper functions in action.yml. The 'Determine version' step (line ~68) and 'Derive context' step (lines ~152-161) both had a write_output helper that used `printf '%s\n' "$value"` without sanitization. Both helpers were replaced with a sanitized version: `safe_value=$(printf '%s' "$value" | tr -d '\n\r')` followed by `printf '%s=%s\n' "$name" "$safe_value" >> "$GITHUB_OUTPUT"`. This strips newline and carriage return characters from all values before writing to GITHUB_OUTPUT, preventing injection of additional key=value pairs via attacker-controlled inputs.

