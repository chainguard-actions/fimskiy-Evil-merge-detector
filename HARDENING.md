<!-- markdownlint-disable -->

# Hardening Report: fimskiy--Evil-merge-detector/v0.1.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **fimskiy--Evil-merge-detector/v0.1.9** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The `run:` block in action.yml directly interpolates `${{ github.action_path }}` into the shell command string (rule a). Any `${{ ... }}` expression inside a `run:` script undergoes YAML template substitution before the shell sees it, making it a script-injection risk. The offending line is: `run: ${{ github.action_path }}/action/entrypoint.sh`. This should be replaced with the environment variable `$GITHUB_ACTION_PATH` instead.

Locations:

- `action.yml:38`

### unpinned-uses (severity: high)

The `uses:` reference `github/codeql-action/upload-sarif@v3` uses a mutable tag (`@v3`) instead of a full 40-character SHA commit hash. This is vulnerable to supply-chain attacks if the tag is moved to a different commit. It should be pinned to a specific SHA, e.g. `github/codeql-action/upload-sarif@<40-char-sha> # v3`.

Locations:

- `action.yml:47`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed two findings in hardened/action/action.yml: (1) script-injection: replaced `${{ github.action_path }}` with the built-in `$GITHUB_ACTION_PATH` environment variable in the run: block, eliminating the template expression interpolation risk; (2) unpinned-uses: pinned `github/codeql-action/upload-sarif@v3` to its full commit SHA `1190a975f95ce23525efb6a3fc21ea29567c1b52` with the original tag preserved as a comment.

