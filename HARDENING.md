<!-- markdownlint-disable -->

# Hardening Report: fimskiy--Evil-merge-detector/v0.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fimskiy--Evil-merge-detector/v0.1.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is interpolated directly inside a run: shell command string. In action.yml, `${{ github.action_path }}` is used directly as part of the shell command (`run: ${{ github.action_path }}/action/entrypoint.sh`). In ci.yml, `${{ steps.run-action.outputs.found }}` and `${{ steps.run-action.outputs.count }}` are interpolated directly inside a run: block, allowing any value in those step outputs to be injected into the shell command before the shell ever sees it.

Locations:

- `action.yml:37`
- `.github/workflows/ci.yml:43`

### unpinned-uses (severity: high)

Multiple uses: references are pinned to mutable tags rather than full 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the tag is moved. Failing references: action.yml: `github/codeql-action/upload-sarif@v3`. ci.yml: `actions/checkout@v4` (multiple jobs), `actions/setup-go@v5` (multiple jobs), `goreleaser/goreleaser-action@v6`.

Locations:

- `action.yml:46`
- `.github/workflows/ci.yml:14`
- `.github/workflows/ci.yml:21`
- `.github/workflows/ci.yml:26`
- `.github/workflows/ci.yml:57`
- `.github/workflows/ci.yml:62`
- `.github/workflows/ci.yml:72`
- `.github/workflows/ci.yml:77`
- `.github/workflows/ci.yml:87`
- `.github/workflows/ci.yml:92`
- `.github/workflows/ci.yml:100`

### missing-permissions (severity: medium)

The workflow file ci.yml has no top-level `permissions:` key, and the jobs `shellcheck`, `test-action`, `test`, and `lint` have no job-level `permissions:` key. Only the `release` job defines permissions. Without explicit permissions, these jobs inherit the default repository permissions (which may include write access), violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings:
1. script-injection (action.yml): Moved `${{ github.action_path }}` from `run:` into `env:` as `ACTION_PATH`, referenced as `$ACTION_PATH` in shell. (ci.yml): Moved `${{ steps.run-action.outputs.found }}` and `${{ steps.run-action.outputs.count }}` into `env:` block as `FOUND`/`COUNT`, referenced as plain env vars in shell.
2. unpinned-uses: Pinned all action references to full SHAs — actions/checkout@34e114876b0b11c390a56381ad16ebd13914f8d5, actions/setup-go@40f1582b2485089dde7abd97c1529aa768e1baff, goreleaser/goreleaser-action@e435ccd777264be153ace6237001ef4d979d3a7a, github/codeql-action/upload-sarif@b7351df727350dca84cb9d725d57dcf5bc82ba26.
3. missing-permissions: Added top-level `permissions: {}` to ci.yml and `permissions: contents: read` to shellcheck, test-action, test, and lint jobs.

