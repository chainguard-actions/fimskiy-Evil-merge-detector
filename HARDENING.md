<!-- markdownlint-disable -->

# Hardening Report: fimskiy--Evil-merge-detector/v0.1.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **fimskiy--Evil-merge-detector/v0.1.5** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated inside a run: shell command string. The line `run: ${{ github.action_path }}/action/entrypoint.sh` injects the github.action_path context value directly into the shell command before the shell ever sees it. Even though github.action_path is not typically attacker-controlled, any ${{ ... }} expression inside a run: block is a script-injection finding per the check rules.

Locations:

- `action.yml:37`

### unpinned-uses (severity: high)

The action uses `github/codeql-action/upload-sarif@v3` which is pinned to a mutable tag (`v3`) rather than a full 40-character commit SHA. This is vulnerable to supply-chain attacks if the tag is moved.

Locations:

- `action.yml:49`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed two findings in action.yml: (1) script-injection on line 37 — moved `${{ github.action_path }}` out of the `run:` shell command into an `env:` variable `ACTION_PATH`, and updated the run command to reference it as `"$ACTION_PATH/action/entrypoint.sh"`; (2) unpinned-uses on line 49 — pinned `github/codeql-action/upload-sarif@v3` to its full commit SHA `dd903d2e4f5405488e5ef1422510ee31c8b32357` with `# v3` comment for readability.

