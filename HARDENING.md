<!-- markdownlint-disable -->

# Hardening Report: fimskiy--Evil-merge-detector/v0.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fimskiy--Evil-merge-detector/v0.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ ... }} expression is interpolated directly inside a run: shell command string. The line `run: ${{ github.action_path }}/action/entrypoint.sh` embeds the github.action_path context value directly into the shell command before the shell ever sees it. Per the check rules, any ${{ ... }} expression directly inside a run: block is a script-injection finding regardless of which context it reads from. The safe alternative is to use the $GITHUB_ACTION_PATH environment variable instead: `run: "$GITHUB_ACTION_PATH/action/entrypoint.sh"`.

Locations:

- `action.yml:57`

### unpinned-uses (severity: high)

The step `uses: github/codeql-action/upload-sarif@v3` references a mutable tag (@v3) rather than a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, creating a supply-chain risk. Pin to a specific SHA, e.g. `uses: github/codeql-action/upload-sarif@<40-char-sha> # v3`.

Locations:

- `action.yml:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed two findings in hardened/action/action.yml: (1) script-injection: replaced `${{ github.action_path }}/action/entrypoint.sh` with `"$GITHUB_ACTION_PATH/action/entrypoint.sh"` to use the safe built-in environment variable instead of interpolating a context expression directly into the shell command; (2) unpinned-uses: pinned `github/codeql-action/upload-sarif@v3` to the full commit SHA `1190a975f95ce23525efb6a3fc21ea29567c1b52 # v3`.

