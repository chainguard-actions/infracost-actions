<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--diff/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--diff/scanner/v0.2.7** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The 'Determine version' step writes the user-controlled input `inputs.version` (via env var `$VERSION`) to `$GITHUB_OUTPUT` using `printf '%s\n' "$value"` inside the `write_output()` helper. This does NOT strip newlines (`tr -d '\n\r'` is absent), so an attacker can supply a newline-containing value for `inputs.version` to inject arbitrary key=value pairs into `$GITHUB_OUTPUT`. The values `version` and `tag` (e.g. `write_output "version" "$VERSION"` and `write_output "tag" "scanner/v${VERSION}"`) are both affected.

Locations:

- `action.yml:56`

### github-env-injection (severity: high)

The 'Derive context' step writes multiple values derived from untrusted `inputs.*` and `github.event.*` sources to `$GITHUB_OUTPUT` via the same unsanitized `write_output()` helper (no `tr -d '\n\r'` step). Affected writes include: `owner` (from `inputs.github-owner`), `repo` (from `inputs.github-repo`), `pr` (from `inputs.pr-number` / `github.event.pull_request.number`), `repo-url` (from `inputs.repo-url`), `pr-title` (from `github.event.pull_request.title`), `pr-author` (from `github.event.pull_request.user.login`), `pr-labels` (from `github.event.pull_request.labels.*.name`), and `pr-status` (from `inputs.pr-status`). Any of these can contain attacker-controlled newlines that poison `$GITHUB_OUTPUT`.

Locations:

- `action.yml:152`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed both occurrences of the write_output() helper function in action.yml. In both the 'Determine version' step and the 'Derive context' step, added newline sanitization by introducing a `safe_value` local variable that strips \n and \r characters via `printf '%s' "$value" | tr -d '\n\r'` before writing to $GITHUB_OUTPUT. This prevents attacker-controlled inputs (inputs.version, inputs.github-owner, inputs.github-repo, inputs.pr-number, inputs.repo-url, inputs.pr-status, github.event.pull_request.title, github.event.pull_request.user.login, github.event.pull_request.labels.*.name) from injecting arbitrary key=value pairs into $GITHUB_OUTPUT via embedded newlines.

