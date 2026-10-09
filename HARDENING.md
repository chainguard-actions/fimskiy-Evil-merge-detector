<!-- markdownlint-disable -->

# Hardening Report: fimskiy--Evil-merge-detector/v0.1.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fimskiy--Evil-merge-detector/v0.1.5** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

In action.yml, the `run:` block directly interpolates `${{ github.action_path }}` as a shell command string: `run: ${{ github.action_path }}/action/entrypoint.sh`. Per sub-rule (a), any `${{ ... }}` expression inside a `run:` block flows through YAML template substitution before the shell processes it, making it a script-injection risk. The safe alternative is to use the pre-set environment variable `$GITHUB_ACTION_PATH` instead.

Locations:

- `action.yml:43`

### unpinned-uses (severity: high)

The action.yml references `github/codeql-action/upload-sarif@v3`, which uses a mutable version tag (`@v3`) rather than a full 40-character commit SHA. This is vulnerable to supply-chain attacks if the tag is moved. It should be pinned to a specific SHA, e.g. `github/codeql-action/upload-sarif@<40-char-sha> # v3`.

Locations:

- `action.yml:52`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed two findings in hardened/action/action.yml: (1) script-injection: replaced `${{ github.action_path }}/action/entrypoint.sh` with `$GITHUB_ACTION_PATH/action/entrypoint.sh` to avoid YAML template substitution of the expression in the run: block; (2) unpinned-uses: pinned `github/codeql-action/upload-sarif@v3` to its full commit SHA `9f759ee644a3e7c15c1390abf49868036c00067b` with `# v3` comment for readability.

