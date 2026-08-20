<!-- markdownlint-disable -->

# Hardening Report: codespell-project--actions-codespell/v2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **codespell-project--actions-codespell/v2.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of ${{ matrix.codespell_pip_version }} inside `run:` shell command strings. Although `matrix.*` is defined in the workflow, any `${{ ... }}` expression interpolated directly into a `run:` block is a script-injection risk — the value flows through YAML template substitution before the shell sees it, allowing shell metacharacters to be injected. Offending lines: `run: pip install ${{ matrix.codespell_pip_version }}` and `run: pip3 --quiet --quiet install ${{ matrix.codespell_pip_version }}`. Fix: move the value into an `env:` variable and reference it as a quoted shell variable, e.g. `"$CODESPELL_PIP_VERSION"`.

Locations:

- `.github/workflows/testing.yml:26`
- `.github/workflows/testing.yml:70`

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files use mutable tag refs instead of pinned 40-character SHA commits, making the workflows vulnerable to supply-chain attacks if the referenced tags are moved. Failing references: publish_master.yml — `actions/checkout@v5`; publish_stable.yml — `actions/checkout@v5`; testing.yml — `actions/checkout@v5` (multiple jobs), `actions/setup-python@v6` (multiple jobs). All should be pinned to full SHA digests, e.g. `actions/checkout@<40-hex-sha> # v5`.

Locations:

- `.github/workflows/publish_master.yml:14`
- `.github/workflows/publish_stable.yml:9`
- `.github/workflows/testing.yml:21`
- `.github/workflows/testing.yml:23`
- `.github/workflows/testing.yml:43`
- `.github/workflows/testing.yml:49`
- `.github/workflows/testing.yml:55`
- `.github/workflows/testing.yml:65`
- `.github/workflows/testing.yml:67`

### missing-permissions (severity: medium)

None of the three workflow files define a top-level `permissions:` key, and none of the individual jobs define job-level `permissions:` keys. Without explicit permissions, workflows run with the default (often broad) token permissions. Each workflow should declare minimal required permissions, e.g. `permissions: read-all` at the top level or specific scopes per job.

Locations:

- `.github/workflows/testing.yml:1`
- `.github/workflows/publish_master.yml:1`
- `.github/workflows/publish_stable.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across the three workflow files:

1. script-injection (testing.yml lines 26, 70): Moved `${{ matrix.codespell_pip_version }}` out of `run:` blocks into `env:` blocks as `CODESPELL_PIP_VERSION`, referenced as `"$CODESPELL_PIP_VERSION"` in shell commands.

2. unpinned-uses: Pinned all action references to full SHA commits:
   - actions/checkout@v5 → @fbc6f3992d24b796d5a048ff273f7fcc4a7b6c09 # v5 (publish_master.yml, publish_stable.yml, testing.yml x4)
   - actions/setup-python@v6 → @ece7cb06caefa5fff74198d8649806c4678c61a1 # v6 (testing.yml x2)

3. missing-permissions: Added `permissions: {}` at the top level of all three workflow files. For publish_master.yml and publish_stable.yml, added job-level `permissions: packages: write, contents: read` since those jobs push Docker images to GitHub Package Registry.

