<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/scanner/v0.2.7** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The 'Determine version' step writes the user-controlled input `inputs.version` (via env var `$VERSION`) to `$GITHUB_OUTPUT` using the `write_output()` helper function. The helper uses `printf '%s\n' "$value"` to write the value, but does NOT apply the required `tr -d '\n\r'` sanitization before writing. An attacker can supply a newline-containing value for `inputs.version` to inject arbitrary key=value pairs into GITHUB_OUTPUT. The write occurs at the `write_output "version" "$VERSION"` and `write_output "tag" "scanner/v${VERSION}"` calls.

Locations:

- `action.yml:55`
- `action.yml:56`

### github-env-injection (severity: high)

The 'Derive context' step writes the user-controlled input `inputs.repo-url` (via env var `$INPUT_REPO_URL`, assigned to `$REPO_URL`) to `$GITHUB_OUTPUT` using the same `write_output()` helper function. The helper uses `printf '%s\n' "$value"` without the required `tr -d '\n\r'` sanitization. An attacker can supply a newline-containing value for `inputs.repo-url` to inject arbitrary key=value pairs into GITHUB_OUTPUT. The write occurs at the `write_output "repo-url" "$REPO_URL"` call.

Locations:

- `action.yml:100`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed both github-env-injection findings in action.yml by updating the write_output() helper function in both the 'Determine version' step (lines 55-56) and the 'Derive context' step (line 100). In each helper, added a safe_value local variable that strips carriage returns and newlines using `printf '%s' "$value" | tr -d '\n\r'` before writing to $GITHUB_OUTPUT. This prevents injection of arbitrary key=value pairs via newline-containing user-controlled inputs (inputs.version and inputs.repo-url).

