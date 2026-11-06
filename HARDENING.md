<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/v3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/v3** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses mutable tag references instead of full commit SHAs: 'uses: actions/checkout@v3' (line 25), 'uses: infracost/actions/setup@v3' (line 32), 'uses: actions/checkout@v3' (line 45).

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:45`

### script-injection (severity: high)

Sub-rule (a): the 'Post Infracost comment' step puts GitHub expressions directly into its run: script: '--github-token=${{github.token}}', '--pull-request=${{github.event.pull_request.number}}', '--behavior=${{inputs.behavior}}'. The inputs.behavior value can be set by whoever calls the action, and it is substituted into the shell script without quoting. Fix: pass each value through an env: variable and expand it double-quoted, e.g. --behavior="$BEHAVIOR".

Locations:

- `action.yml:55`
- `action.yml:65`
- `action.yml:66`
- `action.yml:67`

### static-inline-injection (severity: high)

shell injection: expression "${{inputs.behavior}}" appears directly in run: block of step "Post Infracost comment"; move to env: map

Locations:

- `action.yml:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Pinned actions/checkout@v3 (a37ce912...) and infracost/actions/setup@v3 (e9d6e6cd...) to full commit SHAs, with the tag kept in a comment. In 'Post Infracost comment', moved github.token, the pull_request number and inputs.behavior into env: (GITHUB_TOKEN_VALUE, PR_NUMBER, BEHAVIOR). The script now uses them double-quoted. Left as upstream wrote it: the placeholder action's run steps have no shell:, and the setup step passes inputs.api_key while the input is named api-key.

### Iteration 2

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

In the 'Post Infracost comment' step, replaced ${{github.token}}, ${{github.event.pull_request.number}}, ${{inputs.behavior}} with the existing env vars "$GITHUB_TOKEN_VALUE", "$PR_NUMBER", "$BEHAVIOR", and quoted "$GITHUB_REPOSITORY".

