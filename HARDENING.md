<!-- markdownlint-disable -->

# Hardening Report: fimskiy--Evil-merge-detector/v0.1.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fimskiy--Evil-merge-detector/v0.1.9** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated inside a run: shell command string. In action.yml, the step 'Detect evil merges' uses `run: ${{ github.action_path }}/action/entrypoint.sh`. Any ${{ ... }} expression directly inside a run: block is a script-injection risk because the value is substituted into the shell command string before the shell parses it. The safe alternative is to use the $GITHUB_ACTION_PATH environment variable instead: `run: "$GITHUB_ACTION_PATH/action/entrypoint.sh"`

Locations:

- `action.yml:14`

### unpinned-uses (severity: high)

The action uses an unpinned tag reference `github/codeql-action/upload-sarif@v3` instead of a full 40-character commit SHA. Mutable tags can be moved to point to different (potentially malicious) commits, enabling supply-chain attacks. Pin to a specific SHA, e.g. `github/codeql-action/upload-sarif@<40-char-sha> # v3`.

Locations:

- `action.yml:47`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed two findings in hardened/action/action.yml: (1) script-injection: replaced `${{ github.action_path }}/action/entrypoint.sh` with `"$GITHUB_ACTION_PATH/action/entrypoint.sh"` to use the safe environment variable instead of direct expression interpolation; (2) unpinned-uses: pinned `github/codeql-action/upload-sarif@v3` to the full commit SHA `9f759ee644a3e7c15c1390abf49868036c00067b # v3`.

