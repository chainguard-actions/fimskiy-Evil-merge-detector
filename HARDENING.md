<!-- markdownlint-disable -->

# Hardening Report: fimskiy--Evil-merge-detector/v0.1.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **fimskiy--Evil-merge-detector/v0.1.9** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml references 'github/codeql-action/upload-sarif@v3' using a mutable tag (@v3) rather than a full 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without any change to this file.

Locations:

- `action.yml:48`

### script-injection (severity: high)

Sub-rule (a): The run: block in the 'Detect evil merges' step directly interpolates the GitHub Actions expression ${{ github.action_path }} into the shell command string: `run: ${{ github.action_path }}/action/entrypoint.sh`. Any ${{ ... }} expression inside a run: block is subject to YAML template substitution before the shell sees it, making it a script-injection risk. The safe alternative is to use the $GITHUB_ACTION_PATH environment variable instead.

Locations:

- `action.yml:38`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed two findings in action.yml: (1) script-injection on line 38 — replaced `${{ github.action_path }}` with the safe `$GITHUB_ACTION_PATH` environment variable in the run: block; (2) unpinned-uses on line 48 — pinned `github/codeql-action/upload-sarif@v3` to its full commit SHA `dd903d2e4f5405488e5ef1422510ee31c8b32357` with a `# v3` comment preserved for readability.

