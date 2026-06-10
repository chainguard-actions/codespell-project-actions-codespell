<!-- markdownlint-disable -->

# Hardening Report: codespell-project--actions-codespell/v2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **codespell-project--actions-codespell/v2.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: entrypoint.sh expands multiple workflow-controlled INPUT_* environment variables unquoted inside shell commands, allowing an attacker to inject shell metacharacters via action inputs. Specifically:
- `${INPUT_BUILTIN}` is appended unquoted to `command_args` (line ~14)
- `${INPUT_CONFIG}` is appended unquoted to `command_args` (line ~24)
- `${INPUT_EXCLUDE_FILE}` is appended unquoted to `command_args` (line ~29)
- `${INPUT_IGNORE_WORDS_FILE}` is appended unquoted to `command_args` (line ~34)
- `${INPUT_IGNORE_WORDS_LIST}` is appended unquoted to `command_args` (line ~39)
- `${INPUT_URI_IGNORE_WORDS_LIST}` is appended unquoted to `command_args` (line ~44)
- `${INPUT_SKIP}` is appended unquoted to `command_args` (line ~49)
- `${command_args}` and `${INPUT_PATH}` are both expanded unquoted in the final backtick `codespell` invocation (line ~53)
All these variables derive from action `inputs:` which are workflow-controlled and therefore untrusted. An attacker supplying a value like `; malicious_command` via any of these inputs could achieve command injection. All expansions must be double-quoted (e.g. `"${INPUT_PATH}"`, `"${command_args}"`) or the arguments must be passed via an array to avoid word-splitting and glob expansion.

Locations:

- `entrypoint.sh:14`
- `entrypoint.sh:24`
- `entrypoint.sh:29`
- `entrypoint.sh:34`
- `entrypoint.sh:39`
- `entrypoint.sh:44`
- `entrypoint.sh:49`
- `entrypoint.sh:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Rewrote entrypoint.sh to eliminate all unquoted variable expansions. Replaced the string-based `command_args` accumulation pattern with POSIX sh positional parameters (`set --` / `set -- "$@" ...`). Each INPUT_* variable is now double-quoted when added to the argument list (e.g., `set -- "$@" --builtin "${INPUT_BUILTIN}"`), and the final codespell invocation uses `codespell --count "$@" "${INPUT_PATH}"` — all properly quoted. This prevents word-splitting, glob expansion, and shell metacharacter injection from any attacker-controlled action input.

