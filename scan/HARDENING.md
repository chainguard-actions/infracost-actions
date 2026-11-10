<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/v5.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/v5.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The 'Resolve image tag' step (id: image) passes REF, which comes from the untrusted inputs.version (env VERSION), to write_output. That function writes the value to $GITHUB_OUTPUT between heredoc delimiters built as delimiter="ghadelimiter_${RANDOM}_${RANDOM}_${RANDOM}_${RANDOM}". The check says $RANDOM does not qualify as a random delimiter because it can be predicted, and the value is not passed through printf '%s' ... | tr -d '\n\r' before the write. The regex checks on VERSION (anchored ^...$, no newlines possible) make this hard to exploit in practice, but the pattern still fails the check. Fix: build the delimiter from /dev/urandom (e.g. od -An -N16 -tx1 /dev/urandom | tr -d ' \n'), or strip newlines from the value with printf '%s' "$value" | tr -d '\n\r' before writing.

Locations:

- `action.yml:36`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/scan/action.yml b/scan/action.yml
index 9d46ec4..1f7b53d 100644
--- a/scan/action.yml
+++ b/scan/action.yml
@@ -1,6 +1,5 @@
 name: "Infracost Scan"
 description: "Scan infrastructure code and upload baseline cost data to the Infracost dashboard."
-
 inputs:
   api-key:
     description: "Infracost API key for authentication."
@@ -18,10 +17,26 @@ inputs:
   repo-url:
     description: "Repository URL for metadata."
     required: false
-
 runs:
   using: composite
   steps:
     - name: Resolve image tag
       id: image
       shell: bash
@@ -83,7 +98,6 @@ runs:
             ;;
         esac
         write_output "ref" "$REF"
-
     - name: Run scanner
       shell: bash
       env:

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection, prior-fix

**Notes:**

action.yml: write_output now builds its heredoc delimiter from /dev/urandom (od -An -N16 -tx1 | tr -d ' \n') instead of $RANDOM. REF also has its newlines stripped with printf '%s' | tr -d '\n\r' before it is written, which changes nothing for this single-line value. prior-fix: the earlier patch only removed blank lines (its added lines did not appear in the diff), so there was no security change to re-apply. All ${{ }} expressions were already in env: blocks.

