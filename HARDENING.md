<!-- markdownlint-disable -->

# Hardening Report: fimskiy--Evil-merge-detector/v0.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fimskiy--Evil-merge-detector/v0.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated inside a `run:` shell command string. The line `run: ${{ github.action_path }}/action/entrypoint.sh` embeds `${{ github.action_path }}` directly into the shell command via YAML template substitution before the shell ever sees it. The safe alternative is to use the pre-set environment variable `$GITHUB_ACTION_PATH` instead: `run: "$GITHUB_ACTION_PATH/action/entrypoint.sh"`.

Locations:

- `action.yml:34`

### unpinned-uses (severity: high)

The following `uses:` reference is pinned to a mutable tag rather than an immutable 40-character SHA commit hash, making it vulnerable to supply-chain attacks if the tag is moved: `uses: github/codeql-action/upload-sarif@v3`. It should be pinned to a full SHA, e.g. `uses: github/codeql-action/upload-sarif@<40-char-sha> # v3`.

Locations:

- `action.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed two findings in hardened/action/action.yml: (1) script-injection on line 34 — replaced `${{ github.action_path }}/action/entrypoint.sh` with `"$GITHUB_ACTION_PATH/action/entrypoint.sh"` to use the pre-set environment variable instead of a template expression; (2) unpinned-uses on line 46 — pinned `github/codeql-action/upload-sarif@v3` to full SHA `9f759ee644a3e7c15c1390abf49868036c00067b` with a `# v3` comment.

