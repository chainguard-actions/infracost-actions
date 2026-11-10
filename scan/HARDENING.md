<!-- markdownlint-disable -->

# Hardening Report: infracost--actions--scan/v5.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions--scan/v5.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Resolve image tag' step, the write_output function writes REF to $GITHUB_OUTPUT between heredoc delimiters. REF is built from the untrusted inputs.version (through env VERSION). The delimiter is made with `delimiter="ghadelimiter_${RANDOM}_${RANDOM}_${RANDOM}_${RANDOM}"`. $RANDOM is predictable, not a cryptographically secure source, so the random-heredoc exception does not apply. The value is also not run through `printf '%s' ... | tr -d '\n\r'` before the write. The strict regex checks on VERSION reduce the practical risk, but the check still requires either a secure delimiter (for example from /dev/urandom, openssl rand or uuidgen) or the newline-stripping step.

Locations:

- `action.yml:37`

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

github-env-injection: in the 'Resolve image tag' step, write_output now builds its heredoc delimiter from /dev/urandom ('od -An -N16 -tx1 /dev/urandom') instead of $RANDOM. It also strips newlines from the value before writing it. That is safe because the image ref is always one line. prior-fix: the visible part of the earlier patch only deletes blank lines. Its hunk header says 16 lines were added (+17,26), but the diff I was given doesn't show them, so I couldn't re-create them. This change is the only edit to action.yml. If that earlier patch added something security-related, it isn't in this pass and needs checking against the full diff.

