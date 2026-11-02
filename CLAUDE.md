# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is the **NerionSoft organization-level `.github` repository**. It defines default GitHub community health files (issue templates, PR templates, contributing guidelines, security policy, code of conduct) and reusable CI/CD workflows that apply across all NerionSoft repositories. There is no application code, build system, or test suite here.

## No Local Build/Test Commands

This repository contains only Markdown, YAML, and JSON files. There are no dependencies to install, no linter to run, and no tests to execute locally. Validation happens via GitHub Actions when workflows are consumed by other repos.

## Branch Model & Workflow

- **`main`** — stable / production; serves as the default for all NerionSoft repos.
- **`integration`** — staging / pre-production; standard changes target this branch via PR.
- Working branches use prefixes: `feat/`, `fix/`, `chore/`, `docs/`, `refactor/`, `hotfix/`.
- Hotfixes may target `main` directly but must be propagated back to `integration`.

## Commit Conventions

Follow **Conventional Commits**: `feat:`, `fix:`, `docs:`, `refactor:`, `chore:`, `test:`, `ci:`.

## Architecture: Workflow Templates

The reusable CI/CD workflows live in **NerionSoft/nerionsoft-cicd** (`reusable-lint-ci.yml`, `reusable-nextjs-ci.yml`, `reusable-python-ci.yml`, `reusable-release.yml`, `reusable-build-push.yml`), not here. This repo only holds:

1. **Workflow templates** (`workflow-templates/`) — starter workflows that appear in the Actions tab of org repos. Each template has a `.yml` workflow calling a nerionsoft-cicd reusable workflow at `@main`, and a `.properties.json` metadata file. They are copied into consuming repos (not called remotely). `nerionsoft-release.yml` passes the org secret `RELEASE_APP_PRIVATE_KEY` so Release Please and the backmerge act as the `nerionsoft-release` GitHub App (their pull requests then run CI).
2. **Community health files** — defaults for every org repo (see Key Files).

## Release Automation

None: this repo has no workflow and no release (exempt from the NerionSoft update strategy). `CHANGELOG.md` is kept for history.

## Key Files

- `CONTRIBUTING.md` — contribution workflow and quality requirements (authoritative source for branch model and PR guidelines).
- `SECURITY.md` — vulnerability reporting process (email security@nerionsoft.com, never public issues).
- `GOVERNANCE.md` — roles and decision-making. All changes require review by at least one maintainer.
- `PULL_REQUEST_TEMPLATE.md` — default PR template (Change, Verification, Risk level, Notes).
- `.github/ISSUE_TEMPLATE/*.yml` — YAML issue forms: bug, feature, CI/CD, docs, security, tech debt.
- `.github/DISCUSSION_TEMPLATE/` — discussion category forms (ideas, Q&A).
- `CODEOWNERS` — all files owned by `@nerionsoft/NST-Core` (applies to this repo only, not inherited).
- `profile/README.md` — GitHub organization profile page.

## Important Considerations

- Changes here propagate as defaults to **all** NerionSoft repositories. Review carefully before modifying any template or policy.
- Reusable workflow changes affect every repo that calls them — treat as a shared API with backwards-compatibility concerns.
- PRs must include: context/motivation, what changed and why, how it was tested, and risk level.
- Security vulnerabilities must never be reported via public issues — use security@nerionsoft.com.
- GitHub Actions are pinned by full commit SHA (not tag) for supply-chain security.
