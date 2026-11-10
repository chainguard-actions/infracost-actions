<!-- markdownlint-disable -->

# Hardening Report: infracost--actions/v5.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **infracost--actions/v5.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action references actions by mutable tags instead of 40-character commit SHAs: 'uses: actions/checkout@v4' (line 25), 'uses: infracost/actions/setup@v3' (line 32), 'uses: actions/checkout@v4' (line 46).

Locations:

- `action.yml:25`
- `action.yml:32`
- `action.yml:46`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index f0f5977..33c8f9d 100644
--- a/action.yml
+++ b/action.yml
@@ -1,7 +1,6 @@
 # This is a placeholder action, see https://github.com/infracost/actions/ for the Infracost actions and examples of how to use them.
 name: Infracost Actions
 description: See cloud cost estimates and FinOps best practices for Terraform in pull requests.
-
 author: infracost
 branding:
   icon: trending-up
@@ -20,19 +19,34 @@ inputs:
 runs:
   using: "composite"
   steps:
     # Checkout the branch you want Infracost to compare costs against, most commonly the target branch.
     - name: Checkout base branch
       uses: actions/checkout@v4
       with:
         ref: '${{ github.event.pull_request.base.ref }}'
-
     # Install the Infracost CLI, see https://github.com/infracost/actions/tree/master/setup
     # for other inputs such as version, and pricing-api-endpoint (for self-hosted users).
     - name: Setup Infracost
       uses: infracost/actions/setup@v3
       with:
         api-key: ${{ inputs.api_key }}
-
     # Generate an Infracost output JSON from the comparison branch, so that Infracost can compare the cost difference.
     - name: Generate Infracost cost estimate baseline
       shell: bash
@@ -40,11 +54,8 @@ runs:
         infracost breakdown --path=examples/terraform-directory/code \
                             --format=json \
                             --out-file=/tmp/infracost-base.json
-
-
     # Checkout the PR branch with your infrastructure changes.
     - uses: actions/checkout@v4
-
     - name: Run Infracost
       shell: bash
       run: |
@@ -52,7 +63,6 @@ runs:
                             --format=json \
                             --compare-to=/tmp/infracost-base.json \
                             --out-file=/tmp/infracost.json
-
     - name: Post Infracost comment
       shell: bash
       env:

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, prior-fix

**Notes:**

Pinned all three `uses:` references in action.yml to full commit SHAs that the lookup tool returned:
- both `actions/checkout@v4` steps now use `11d5960a326750d5838078e36cf38b85af677262 # v4`
- `infracost/actions/setup@v3` now uses `e9d6e6cd65e168e76b0de50ff9957d2fe8bb1832 # v3`

About the prior-fix finding: the part of the earlier patch shown in the request only removes blank lines. Its line counts show it also added 15 lines that the diff leaves out, and it is unclear what those were; I'm assuming they were the same pinning. The steps that run shell commands already pass values through `env:`, so I found no other security change to re-apply. Nothing was tested.

