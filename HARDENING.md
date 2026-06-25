<!-- markdownlint-disable -->

# Hardening Report: fimskiy--Evil-merge-detector/v0.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **fimskiy--Evil-merge-detector/v0.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is interpolated directly inside a run: shell command string. The line `run: ${{ github.action_path }}/action/entrypoint.sh` embeds the github.action_path context directly into the shell command before the shell ever sees it. While github.action_path is not attacker-controlled in the same way as github.head_ref, any ${{ ... }} expression inside a run: block is a script-injection finding per the check rules, as YAML template substitution occurs before shell quoting.

Locations:

- `action.yml:40`

### unpinned-uses (severity: high)

The step `uses: github/codeql-action/upload-sarif@v3` references a mutable tag (@v3) rather than a full 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without the workflow author's knowledge, creating a supply-chain risk.

Locations:

- `action.yml:48`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed two findings in action.yml: (1) script-injection on line 40 — moved ${{ github.action_path }} out of the run: shell command into an env: block as ACTION_PATH, and updated the run command to use "$ACTION_PATH/action/entrypoint.sh"; (2) unpinned-uses on line 48 — pinned github/codeql-action/upload-sarif from the mutable @v3 tag to the full commit SHA @dd903d2e4f5405488e5ef1422510ee31c8b32357 with a # v3 comment for readability.

