<!-- markdownlint-disable -->

# Hardening Report: fimskiy--Evil-merge-detector/v0.1.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fimskiy--Evil-merge-detector/v0.1.9** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags or branch names instead of immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the referenced tag is moved or the branch is updated.

In `action.yml`:
- `github/codeql-action/upload-sarif@v3` (tag)

In `.github/workflows/ci.yml`:
- `actions/checkout@v4` (tag, multiple jobs)
- `actions/setup-go@v5` (tag, multiple jobs)
- `goreleaser/goreleaser-action@v6` (tag)

In `.github/workflows/fly-deploy.yml`:
- `actions/checkout@v4` (tag)
- `superfly/flyctl-actions/setup-flyctl@master` (branch — especially dangerous)

Locations:

- `action.yml:47`
- `.github/workflows/ci.yml:15`
- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:46`
- `.github/workflows/ci.yml:51`
- `.github/workflows/ci.yml:60`
- `.github/workflows/ci.yml:65`
- `.github/workflows/ci.yml:74`
- `.github/workflows/ci.yml:79`
- `.github/workflows/ci.yml:86`
- `.github/workflows/fly-deploy.yml:11`
- `.github/workflows/fly-deploy.yml:12`

### missing-permissions (severity: medium)

Neither `ci.yml` nor `fly-deploy.yml` has a top-level `permissions:` block, and most jobs within them also lack job-level `permissions:` blocks. In `ci.yml`, only the `release` job defines `permissions: contents: write`; the `shellcheck`, `test-action`, `test`, and `lint` jobs have no permissions defined. In `fly-deploy.yml`, the `deploy` job has no permissions defined. Without explicit permissions, jobs run with the default (potentially broad) token permissions.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/fly-deploy.yml:1`

### script-injection (severity: high)

Sub-rule (a) violation: The 'Verify outputs' step in `.github/workflows/ci.yml` directly interpolates `${{ steps.run-action.outputs.found }}` and `${{ steps.run-action.outputs.count }}` inside a `run:` shell script. GitHub Actions expression interpolation happens before the shell parses the script, so if these output values contain shell metacharacters (e.g. injected via crafted repository content processed by the action), they could execute arbitrary commands. The values should be passed via `env:` variables and referenced as `$ENV_VAR` instead.

Offending lines:
  `echo "found=${{ steps.run-action.outputs.found }}"`
  `echo "count=${{ steps.run-action.outputs.count }}"`
  `[[ "${{ steps.run-action.outputs.found }}" != "" ]] || ...`
  `[[ "${{ steps.run-action.outputs.count }}" != "" ]] || ...`

Locations:

- `.github/workflows/ci.yml:40`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings:

1. **unpinned-uses**: Pinned all mutable action references to full 40-char commit SHAs with tag comments:
   - action.yml: github/codeql-action/upload-sarif@v3 → @b7351df727350dca84cb9d725d57dcf5bc82ba26 # v3
   - ci.yml: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 # v4 (all occurrences), actions/setup-go@v5 → @40f1582b2485089dde7abd97c1529aa768e1baff # v5 (all occurrences), goreleaser/goreleaser-action@v6 → @e435ccd777264be153ace6237001ef4d979d3a7a # v6
   - fly-deploy.yml: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 # v4, superfly/flyctl-actions/setup-flyctl@master → @ed8efb33836e8b2096c7fd3ba1c8afe303ebbff1 # master

2. **missing-permissions**: Added top-level `permissions: {}` to both ci.yml and fly-deploy.yml. Added job-level `permissions: contents: read` to shellcheck, test-action, test, lint, and deploy jobs. The release job already had `permissions: contents: write`.

3. **script-injection**: Moved `${{ steps.run-action.outputs.found }}` and `${{ steps.run-action.outputs.count }}` from the run: shell script into an env: block as FOUND and COUNT, then referenced them as plain $FOUND and $COUNT environment variables in the shell script.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script-injection in hardened/action/action.yml line 36: replaced `${{ github.action_path }}/action/entrypoint.sh` with `"$GITHUB_ACTION_PATH/action/entrypoint.sh"`. The `$GITHUB_ACTION_PATH` environment variable is the standard GitHub Actions equivalent and is always set in composite action steps, so no behavior change occurs.

