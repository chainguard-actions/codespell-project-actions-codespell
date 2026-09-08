<!-- markdownlint-disable -->

# Hardening Report: codespell-project--actions-codespell--codespell-problem-matcher/v2.4.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **codespell-project--actions-codespell--codespell-problem-matcher/v2.4.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow file .github/workflows/test.yml contains multiple unpinned `uses:` references that use branch names or version tags instead of full 40-character commit SHAs. Specifically: `actions/checkout@master` (branch ref, used in both jobs), `actions/setup-python@v1` (tag ref, in test-stable), and `actions/setup-python@v4` (tag ref, in test-master). These are vulnerable to supply-chain attacks because the referenced content can change at any time without notice.

Locations:

- `.github/workflows/test.yml:12`
- `.github/workflows/test.yml:13`
- `.github/workflows/test.yml:24`
- `.github/workflows/test.yml:25`

### missing-permissions (severity: medium)

The workflow file .github/workflows/test.yml has no top-level `permissions:` key and neither job (`test-stable`, `test-master`) defines its own `permissions:` block. This means the workflow runs with the default (broad) GitHub token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed .github/workflows/test.yml: (1) Pinned all four unpinned action references to full 40-char commit SHAs — actions/checkout@master→61b9e3751b92087fd0b06925ba6dd6314e06f089, actions/setup-python@v1→0f07f7f756721ebd886c2462646a35f78a8bc4de, actions/setup-python@v4→7f4fc3e22c37d6ff65e88745f38bd3157c663f7c — with original tag/branch names preserved as inline comments. (2) Added top-level `permissions: {}` to enforce least privilege since the workflow only runs tests and requires no GitHub token permissions.

