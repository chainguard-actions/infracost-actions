<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--diff/v5.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--diff/v5.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

Step 'Resolve image tag' (id: image) writes REF to $GITHUB_OUTPUT between heredoc delimiters. REF is built from the caller-controlled inputs.version (passed in as VERSION). The delimiter is built with `delimiter="ghadelimiter_${RANDOM}_${RANDOM}_${RANDOM}_${RANDOM}"`. $RANDOM is not a cryptographically secure source: it is a 15-bit predictable PRNG. Under this check that makes it the same as a fixed delimiter (case f). The value is also never passed through `printf '%s' ... | tr -d '\n\r'`. The regex/case checks on VERSION do reduce the real risk. To fix it, build the delimiter from /dev/urandom, e.g. `delimiter="ghadelimiter_$(od -An -N16 -tx1 /dev/urandom | tr -d ' \n')"`, or write a newline-stripped single-line value: `printf 'ref=%s\n' "$(printf '%s' "$REF" | tr -d '\n\r')" >> "$GITHUB_OUTPUT"`.

Locations:

- `action.yml:55`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/diff/action.yml b/diff/action.yml
index 9327697..687d3b9 100644
--- a/diff/action.yml
+++ b/diff/action.yml
@@ -1,6 +1,5 @@
 name: "Infracost Scan"
 description: "Scan infrastructure code for cost changes, post a PR comment, and manage PR status in the Infracost dashboard."
-
 inputs:
   api-key:
     description: "Infracost API key for authentication."
@@ -37,10 +36,26 @@ inputs:
   repo-url:
     description: "Repository URL. Selects the repository the comment is posted to and the source links, so github-token needs write access to it. Defaults to the current repository."
     required: false
-
 runs:
   using: composite
   steps:
     - name: Resolve image tag
       id: image
       shell: bash
@@ -102,7 +117,6 @@ runs:
             ;;
         esac
         write_output "ref" "$REF"
-
     # Only the status is derived here. The VCS contract is repeated in each
     # step's env: GITHUB_ENV would export it to the whole caller job.
     - name: Derive pull request status
@@ -155,7 +169,6 @@ runs:
         printf 'pr_status=%s\n' "$PR_STATUS" >> "$GITHUB_OUTPUT"
         printf 'scan=%s\n' "$SCAN" >> "$GITHUB_OUTPUT"
         echo "Infracost pull request status: ${PR_STATUS:-none}, scan: ${SCAN}"
-
     - name: Run scanner
       if: steps.context.outputs.scan == 'true'
       shell: bash
@@ -302,7 +315,6 @@ runs:
           echo "::notice::This step ran inside a container. If the failure above names ${GITHUB_WORKSPACE}, the host Docker daemon cannot see it — drop container: from the job, or use the container: job documented in infracost/ci instead of this action."
         fi
         exit "$STATUS"
-
     # The same repository URL and PR ID expressions as above: the dashboard is
     # keyed on the PR URL built from them, so the two steps must agree.
     - name: Update PR status

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection, prior-fix

**Notes:**

action.yml: in the 'Resolve image tag' step, write_output no longer uses a heredoc with a delimiter built from ${RANDOM}. It now writes `name=value` with CR/LF removed (`printf '%s' | tr -d '\n\r'`), reading the value into a variable first and stripping it separately. REF is already restricted to a validated tag or digest, so the value doesn't change. The visible part of the prior-fix patch only removed blank lines, and its added lines in the image step weren't shown. This output fix should cover its security purpose. No other changes were needed: all expressions are already passed through env: and there are no `uses:` references to pin.

