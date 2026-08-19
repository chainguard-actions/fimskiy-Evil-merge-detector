<!-- markdownlint-disable -->

# Hardening Report: fimskiy--Evil-merge-detector/v0.1.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fimskiy--Evil-merge-detector/v0.1.5** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ ... }} expression is interpolated directly inside a run: shell command. In action.yml line 42, `run: ${{ github.action_path }}/action/entrypoint.sh` embeds the github.action_path context directly in the shell command string. In .github/workflows/ci.yml lines 51-54, the 'Verify outputs' step interpolates `${{ steps.run-action.outputs.found }}` and `${{ steps.run-action.outputs.count }}` directly inside a run: block — these step outputs flow through YAML template substitution before the shell sees them and could contain shell metacharacters.

Locations:

- `action.yml:42`
- `.github/workflows/ci.yml:51`
- `.github/workflows/ci.yml:52`
- `.github/workflows/ci.yml:53`
- `.github/workflows/ci.yml:54`

### unpinned-uses (severity: high)

Multiple uses: references are pinned to mutable tags or branch names instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks. Failing references:
- action.yml: `github/codeql-action/upload-sarif@v3` (line 53)
- .github/workflows/ci.yml: `actions/checkout@v4` (lines 17, 24, 59, 74, 93), `actions/setup-go@v5` (lines 28, 61, 76, 97), `goreleaser/goreleaser-action@v6` (line 101)
- .github/workflows/fly-deploy.yml: `actions/checkout@v4` (line 14), `superfly/flyctl-actions/setup-flyctl@master` (line 15) — @master is especially dangerous as it tracks a moving branch head

Locations:

- `action.yml:53`
- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:59`
- `.github/workflows/ci.yml:74`
- `.github/workflows/ci.yml:76`
- `.github/workflows/ci.yml:93`
- `.github/workflows/ci.yml:97`
- `.github/workflows/ci.yml:101`
- `.github/workflows/fly-deploy.yml:14`
- `.github/workflows/fly-deploy.yml:15`

### missing-permissions (severity: medium)

Workflow files are missing permissions declarations. In .github/workflows/ci.yml there is no top-level permissions: key, and the jobs shellcheck, test-action, test, and lint all lack job-level permissions: blocks (only the release job has permissions). This means those jobs run with the default, overly-broad token permissions. In .github/workflows/fly-deploy.yml there is no top-level permissions: key and the deploy job has no job-level permissions: block.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/fly-deploy.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across action.yml, .github/workflows/ci.yml, and .github/workflows/fly-deploy.yml:

1. script-injection: (a) In action.yml, moved github.action_path into the env: block as ACTION_PATH and referenced it as $ACTION_PATH in the run: command. (b) In ci.yml 'Verify outputs' step, moved steps.run-action.outputs.found and steps.run-action.outputs.count into an env: block as FOUND and COUNT, then used plain env var references in the shell script.

2. unpinned-uses: Pinned all action references to full 40-char SHAs: actions/checkout@v4→34e114876b0b11c390a56381ad16ebd13914f8d5, actions/setup-go@v5→40f1582b2485089dde7abd97c1529aa768e1baff, goreleaser/goreleaser-action@v6→e435ccd777264be153ace6237001ef4d979d3a7a, github/codeql-action/upload-sarif@v3→b7351df727350dca84cb9d725d57dcf5bc82ba26, superfly/flyctl-actions/setup-flyctl@master→ed8efb33836e8b2096c7fd3ba1c8afe303ebbff1.

3. missing-permissions: Added top-level 'permissions: {}' to both ci.yml and fly-deploy.yml. Added job-level 'permissions: { contents: read }' to shellcheck, test-action, test, lint (ci.yml) and deploy (fly-deploy.yml) jobs. The release job already had appropriate 'contents: write' permissions.

