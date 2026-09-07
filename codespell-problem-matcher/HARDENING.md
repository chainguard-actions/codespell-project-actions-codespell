<!-- markdownlint-disable -->

# Hardening Report: codespell-project--actions-codespell--codespell-problem-matcher/v2.4.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **codespell-project--actions-codespell--codespell-problem-matcher/v2.4.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow file .github/workflows/test.yml contains four unpinned `uses:` references that use branch names or version tags instead of full 40-character commit SHA digests. This exposes the workflow to supply-chain attacks if the referenced action is compromised or the tag/branch is moved. Failing references: `actions/checkout@master` (lines 13 and 27), `actions/setup-python@v1` (line 14), `actions/setup-python@v4` (line 28). Each should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/test.yml:13`
- `.github/workflows/test.yml:14`
- `.github/workflows/test.yml:27`
- `.github/workflows/test.yml:28`

### missing-permissions (severity: medium)

The workflow file .github/workflows/test.yml has no top-level `permissions:` key, and neither of its jobs (`test-stable`, `test-master`) defines a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, packages, etc.). A minimal `permissions:` block (e.g. `contents: read`) should be added at the top level or to each job.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed .github/workflows/test.yml: (1) Pinned all four unpinned action references to full 40-character commit SHAs — actions/checkout@master (×2) → @61b9e3751b92087fd0b06925ba6dd6314e06f089, actions/setup-python@v1 → @0f07f7f756721ebd886c2462646a35f78a8bc4de, actions/setup-python@v4 → @7f4fc3e22c37d6ff65e88745f38bd3157c663f7c. (2) Added top-level `permissions: contents: read` block to restrict the GITHUB_TOKEN to the minimum required access.

