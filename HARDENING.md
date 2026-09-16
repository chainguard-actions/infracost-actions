<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/scanner/v0.2.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/scanner/v0.2.7** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The root action.yml references three action steps using mutable version tags instead of pinned 40-character SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved: `uses: actions/checkout@v4` (lines 25 and 46) and `uses: infracost/actions/setup@v3` (line 32).

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:46`

### github-env-injection (severity: high)

In diff/action.yml, the 'Determine version' step writes the untrusted input `inputs.version` (via shell variable `$VERSION`) to $GITHUB_OUTPUT using `printf '%s\n' "$value"` inside the `write_output` helper — without the required `printf '%s' ... | tr -d '\n\r'` sanitization. The 'Derive context' step similarly writes multiple untrusted values sourced from `inputs.*` (github-owner, github-repo, pr-number, repo-url, pr-status, base-path, head-path) and `github.*` (event.pull_request.title, event.pull_request.user.login, event.pull_request.labels, event_name, event.action, event.pull_request.merged) to $GITHUB_OUTPUT via the same unsanitized `write_output` function. The heredoc-delimiter approach prevents fake-entry injection but does not strip embedded newlines from the values themselves.

Locations:

- `diff/action.yml:72`
- `diff/action.yml:73`
- `diff/action.yml:155`
- `diff/action.yml:156`
- `diff/action.yml:157`
- `diff/action.yml:158`
- `diff/action.yml:159`
- `diff/action.yml:160`
- `diff/action.yml:161`
- `diff/action.yml:162`

### github-env-injection (severity: high)

In scan/action.yml, the 'Determine version' step writes the untrusted input `inputs.version` (via shell variable `$VERSION`) to $GITHUB_OUTPUT using `printf '%s\n' "$value"` inside the `write_output` helper — without the required `printf '%s' ... | tr -d '\n\r'` sanitization. The 'Derive context' step similarly writes `$REPO_URL` (sourced from `inputs.repo-url`) to $GITHUB_OUTPUT via the same unsanitized `write_output` function.

Locations:

- `scan/action.yml:57`
- `scan/action.yml:58`
- `scan/action.yml:100`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed 3 findings: (1) Pinned all 3 unpinned action references in action.yml to full 40-char SHAs: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 (both occurrences) and infracost/actions/setup@v3 → @e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832. (2) Fixed github-env-injection in diff/action.yml by adding `safe_value=$(printf '%s' "$value" | tr -d '\n\r')` sanitization to both write_output helper functions ('Determine version' and 'Derive context' steps). (3) Fixed github-env-injection in scan/action.yml by adding the same sanitization to both write_output helper functions ('Determine version' and 'Derive context' steps). The heredoc-delimiter approach was already in place to prevent fake-entry injection; the added newline stripping prevents embedded newlines in untrusted input values from being interpreted as additional GITHUB_OUTPUT entries.

