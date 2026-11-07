# NerionSoft GitHub Standards

This repository holds the organization's community health files and its **workflow templates**. The reusable CI/CD workflows themselves live in [NerionSoft/nerionsoft-cicd](https://github.com/NerionSoft/nerionsoft-cicd), whose README documents every input, output and secret.

## Repository structure

```text
workflow-templates/                       # Starter workflows (Actions > New workflow > "By NerionSoft")
  nerionsoft-lint-ci.yml                  # → nerionsoft-cicd reusable-lint-ci.yml
  nerionsoft-nextjs-ci.yml                # → nerionsoft-cicd reusable-nextjs-ci.yml
  nerionsoft-python-ci.yml                # → nerionsoft-cicd reusable-python-ci.yml
  nerionsoft-release.yml                  # → nerionsoft-cicd reusable-release.yml
  nerionsoft-pnpm-overrides-audit.yml     # → nerionsoft-cicd reusable-pnpm-overrides-audit.yml
  <name>.properties.json                  # Template metadata
profile/                                  # Organization profile page
CONTRIBUTING.md, SECURITY.md, ...         # Community health files (defaults for every repo)
```

## How the templates work

Each template is a small file, copied into the repository that adopts it, that calls a reusable workflow of nerionsoft-cicd at `@main`. The logic stays in nerionsoft-cicd: a fix there reaches every repository without touching them. Defaults are listed as comments in each template; uncomment only what differs.

### Reusable workflows at a glance

| Reusable workflow (nerionsoft-cicd) | What it runs | Template | Triggers |
|---|---|---|---|
| [`reusable-lint-ci.yml`](https://github.com/NerionSoft/nerionsoft-cicd#reusable-lint-ciyml-lint-ci) | actionlint, yamllint, markdownlint, SHA-pinned actions check, secret scan (gitleaks), SonarQube (off by default) | `nerionsoft-lint-ci.yml` | Push and pull request to `main` and the integration branch |
| [`reusable-nextjs-ci.yml`](https://github.com/NerionSoft/nerionsoft-cicd#reusable-nextjs-ciyml-nextjs-ci) | Lint, format, typecheck, tests, build, secret scan, then E2E and SonarQube (Node 24, pnpm) | `nerionsoft-nextjs-ci.yml` | Same |
| [`reusable-python-ci.yml`](https://github.com/NerionSoft/nerionsoft-cicd#reusable-python-ciyml-python-ci) | Ruff, mypy, pytest, secret scan, then SonarQube (uv) | `nerionsoft-python-ci.yml` | Same |
| [`reusable-release.yml`](https://github.com/NerionSoft/nerionsoft-cicd#reusable-releaseyml-release) | Release Please, floating tags, backmerge from `main` into `integration`, acting as the `nerionsoft-release` GitHub App | `nerionsoft-release.yml` | Push to `main` |
| [`reusable-build-push.yml`](https://github.com/NerionSoft/nerionsoft-cicd#reusable-build-pushyml-build--push-image) | Build of the OCI image and push to Harbor, tagged with the release version | Commented `build` job in `nerionsoft-release.yml` | After a release, chained on the release job |
| [`reusable-pnpm-overrides-audit.yml`](https://github.com/NerionSoft/nerionsoft-cicd#reusable-pnpm-overrides-audityml-pnpm-overrides-audit) | Checks that each pnpm override is still needed: audit with and without the overrides, fails on stale, absent, not applied, unbounded `>=` or vulnerability-creating entries; warns on `minimumReleaseAgeExclude` entries | `nerionsoft-pnpm-overrides-audit.yml` | Every Monday 05:00 UTC (default branch) and by hand |

Inputs, outputs, secrets and defaults of each one: the [nerionsoft-cicd README](https://github.com/NerionSoft/nerionsoft-cicd#readme).

### Release (`nerionsoft-release.yml`)

Runs on pushes to `main` and calls Release Please, the floating tags and the backmerge into `integration`:

```yaml
jobs:
  release:
    uses: NerionSoft/nerionsoft-cicd/.github/workflows/reusable-release.yml@main
    # Org secret: Release Please and the backmerge act as the nerionsoft-release
    # App, so the release and backmerge pull requests run CI.
    secrets:
      RELEASE_APP_PRIVATE_KEY: ${{ secrets.RELEASE_APP_PRIVATE_KEY }}
```

The `secrets:` block passes the private key of the `nerionsoft-release` GitHub App (org secret; its client ID is the org variable `RELEASE_APP_CLIENT_ID`). Without it the release still works, with the workflow token: the release and backmerge pull requests then only get CI runs waiting for an approval, and a published release triggers no other workflow. Outputs (`release_created`, `tag_name`, `major`, `minor`, `patch`) and inputs: see the nerionsoft-cicd README.

### pnpm overrides audit (`nerionsoft-pnpm-overrides-audit.yml`)

For pnpm repositories. Runs every Monday on the default branch (GitHub runs scheduled workflows there only) and by hand from the Actions tab. While a repository still has stale overrides, set `report-only: true` so the weekly run reports without failing, then remove it once the overrides are cleaned up.

## Workflow templates — reference

Templates live in `workflow-templates/` at the repo root. Each template has two files:

| File | Purpose |
|---|---|
| `<name>.yml` | The workflow YAML (copied into the developer's repo) |
| `<name>.properties.json` | Metadata controlling how it appears in the GitHub UI |

### Properties file format

```json
{
  "name": "My Workflow",
  "description": "Short description shown in the template picker",
  "iconName": "octicon rocket",
  "categories": ["Node.js", "TypeScript"],
  "filePatterns": ["package.json$"]
}
```

| Field | Required | Description |
|---|---|---|
| `name` | Yes | Display name in the template picker |
| `description` | Yes | Description shown below the name |
| `iconName` | No | `"octicon <name>"` for [Octicons](https://primer.style/octicons/), or a custom SVG filename (without `.svg`) placed in `workflow-templates/` |
| `categories` | No | Filters in the picker. Accepts: language names (`JavaScript`, `TypeScript`, `Go`...), workflow types (`Continuous integration`, `Deployment`, `Code Scanning`...) |
| `filePatterns` | No | Regex array matched against files in the repo root. If any matches, the template is **recommended** to the user (e.g., `["package.json$"]` recommends it for Node.js projects) |

### `$default-branch` placeholder

GitHub replaces `$default-branch` in a template with the repository's default branch when the developer adopts it.

The NerionSoft templates do not use it: releases are always cut from `main`, while CI also runs on the integration branch, and a few repositories (Hestia) use `integration` as their default branch. They name the branches explicitly instead:

- CI templates run on pushes and pull requests to `main` and to the integration branch, named either `integration` or `integration/<repo name>` (team convention). A repository has one or the other: git cannot hold both.
- The release template runs on pushes to `main` only, so a release never starts from the integration branch. A repository whose integration branch is `integration/<repo name>` sets `backmerge-branch` to that name.

```yaml
on:
  push:
    branches: [main, integration, 'integration/**']
  pull_request:
    branches: [main, integration, 'integration/**']
```

## Other managed files

- **`CONTRIBUTING.md`** — contribution workflow and quality requirements
- **`SECURITY.md`** — vulnerability reporting (email security@nerionsoft.com, never public issues)
- **`SUPPORT.md`** — where to get help
- **`GOVERNANCE.md`** — roles, decision-making, review requirements
- **`PULL_REQUEST_TEMPLATE.md`** — default PR template
- **`CODE_OF_CONDUCT.md`** — community code of conduct
- **`CODEOWNERS`** — all files owned by `@nerionsoft/NST-Core`

## Important

- Changes here propagate as defaults to **all** NerionSoft repositories
- PRs must include context, what changed, how it was tested, and risk level
- For production, pin reusable workflows to a tag (`@v1`) rather than `@main`
