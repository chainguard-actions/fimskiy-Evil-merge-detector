<!-- markdownlint-disable -->

# Hardening Report: fimskiy--Evil-merge-detector/v0.1.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fimskiy--Evil-merge-detector/v0.1.5** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated inside a run: shell command string. In action.yml, the step 'Detect evil merges' uses `run: ${{ github.action_path }}/action/entrypoint.sh`. Although github.action_path is generally GitHub-controlled, any ${{ ... }} expression directly inside a run: block is a script-injection risk because the value flows through YAML template substitution before the shell ever sees it. The safe alternative is to use the pre-set environment variable `$GITHUB_ACTION_PATH` instead: `run: "$GITHUB_ACTION_PATH/action/entrypoint.sh"`.

Locations:

- `action.yml:42`

### unpinned-uses (severity: high)

The action references `github/codeql-action/upload-sarif@v3`, which uses a mutable version tag (@v3) rather than a full 40-character commit SHA. A supply-chain attacker who compromises the referenced action repository could push malicious code to that tag. It should be pinned to a specific commit SHA, e.g. `github/codeql-action/upload-sarif@<40-char-sha> # v3`.

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed two findings in hardened/action/action.yml: (1) script-injection: replaced `${{ github.action_path }}/action/entrypoint.sh` with `"$GITHUB_ACTION_PATH/action/entrypoint.sh"` to use the pre-set environment variable instead of a template expression in the run: block; (2) unpinned-uses: pinned `github/codeql-action/upload-sarif@v3` to the full commit SHA `1190a975f95ce23525efb6a3fc21ea29567c1b52` with a `# v3` comment for readability.

