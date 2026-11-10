<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--diff/v5.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--diff/v5.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

Step 'Resolve image tag' writes REF, which is built from inputs.version through env VERSION, to $GITHUB_OUTPUT. The write goes through a heredoc whose delimiter is not secure: delimiter="ghadelimiter_${RANDOM}_${RANDOM}_${RANDOM}_${RANDOM}". $RANDOM is a predictable PRNG and does not count as a cryptographically secure random delimiter, and the value is not passed through printf '%s' | tr -d '\n\r'. The regex checks on VERSION reduce the risk, but the write itself is still non-compliant. Fix: build the delimiter from /dev/urandom (e.g. "EOF_$(od -An -N16 -tx1 /dev/urandom | tr -d ' \n')"), or strip newlines and write a single line: safe=$(printf '%s' "$REF" | tr -d '\n\r'); echo "ref=$safe" >> "$GITHUB_OUTPUT".

Locations:

- `action.yml:56`

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

In action.yml, the 'Resolve image tag' step's write_output helper built its GITHUB_OUTPUT heredoc delimiter from $RANDOM, which can be guessed. It now uses "ghadelimiter_$(od -An -N16 -tx1 /dev/urandom | tr -d ' \n')". The value itself is written unchanged. REF has already been checked by the regex for a single-line tag or digest, so nothing else needed to change. The prior-fix diff I was given cut off the part of its 'Resolve image tag' hunk that adds lines. The only changes it shows are deleted blank lines, which have no security effect. I couldn't confirm what the missing lines did, but the step's one remaining security problem was the delimiter, and that is now fixed. All other steps already pass expressions through env:, so I left them alone.

