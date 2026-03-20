# NerionSoft GitHub Standards

This repository defines shared GitHub standards, community health files, and **reusable CI/CD workflows** for all NerionSoft projects.

## Repository structure

```
.github/
  workflows/
    nextjs-ci.yml              # Reusable workflow — Next.js CI pipeline
    release.yml                # Reusable workflow — release-please
  ISSUE_TEMPLATE/              # Issue forms (bug, feature, CI/CD, etc.)
  DISCUSSION_TEMPLATE/         # Discussion category forms

workflow-templates/            # Starter templates (visible in Actions > New workflow)
  nextjs-ci.yml                # Template → calls the reusable CI
  nextjs-ci.properties.json    # Template metadata
  release.yml                  # Template → calls the reusable release
  release.properties.json      # Template metadata
  ci.yml                       # Legacy basic CI template
  ci.properties.json
```

## Reusable workflows

### How it works

The **reusable workflows** (in `.github/workflows/`) contain the centralized CI logic. The **workflow templates** (in `workflow-templates/`) are lightweight wrappers that call these reusable workflows. When a developer creates a new workflow from the template, they get a small file that delegates everything to the shared logic.

```
Developer repo                        This repo (.github)
┌──────────────────┐                  ┌─────────────────────────┐
│ .github/workflows│                  │ .github/workflows/      │
│   ci.yml ────────┼──── calls ──────►│   nextjs-ci.yml         │
│                  │  (workflow_call)  │   (lint, format, type,  │
│                  │                  │    test, build, e2e,    │
│                  │                  │    sonar)               │
└──────────────────┘                  └─────────────────────────┘
```

### Next.js CI (`nextjs-ci.yml`)

Full pipeline: lint, format check, typecheck, test (with coverage), build, E2E (Playwright), SonarCloud.

**Quick setup — from the GitHub UI:**

1. Go to your repo → **Actions** → **New workflow**
2. Find **"Next.js CI"** under "By NerionSoft"
3. Click **Configure** — GitHub copies the template into your repo
4. Commit the file

**Manual setup — create `.github/workflows/ci.yml` in your repo:**

```yaml
name: CI

on:
  push:
    branches: [main, integration]
  pull_request:
    branches: [main, integration]

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  ci:
    uses: NerionSoft/.github/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: "20"
      run-sonar: true
    secrets:
      SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

**Available inputs:**

| Input | Type | Default | Description |
|---|---|---|---|
| `node-version` | `string` | `"20"` | Node.js version |
| `run-lint` | `boolean` | `true` | Run ESLint |
| `run-format` | `boolean` | `true` | Run `pnpm format:check` |
| `run-typecheck` | `boolean` | `true` | Run `tsc --noEmit` |
| `run-test` | `boolean` | `true` | Run Vitest with coverage |
| `run-build` | `boolean` | `true` | Build Next.js app |
| `run-e2e` | `boolean` | `true` | Run Playwright E2E tests |
| `run-sonar` | `boolean` | `false` | Run SonarCloud analysis |

**Secrets:**

| Secret | Required | Description |
|---|---|---|
| `SONAR_TOKEN` | Only if `run-sonar: true` | SonarCloud authentication token |

**Pipeline graph:**

```
lint ──────┐
format ────┤
typecheck ─┼──► build ──► e2e
test ──────┤
     └─────────► sonar
```

**Disabling specific jobs:**

```yaml
jobs:
  ci:
    uses: NerionSoft/.github/.github/workflows/nextjs-ci.yml@main
    with:
      run-e2e: false      # skip Playwright
      run-sonar: false     # skip SonarCloud
      run-format: false    # skip format check
```

**Prerequisites in your project:**

Your `package.json` must define these scripts (matching the jobs you enable):

```json
{
  "scripts": {
    "lint": "next lint",
    "format:check": "prettier --check .",
    "test:coverage": "vitest run --coverage",
    "test:e2e": "playwright test",
    "build": "next build"
  }
}
```

### Release (`release.yml`)

Automated releases using [release-please](https://github.com/googleapis/release-please) with Conventional Commits.

**Setup — create `.github/workflows/release.yml` in your repo:**

```yaml
name: Release

on:
  push:
    branches: [main]

jobs:
  release:
    uses: NerionSoft/.github/.github/workflows/release.yml@main
    with:
      release-type: node
    permissions:
      contents: write
      pull-requests: write
```

**Available inputs:**

| Input | Type | Default | Description |
|---|---|---|---|
| `release-type` | `string` | `"node"` | Release type (`node`, `python`, `go`, etc.) |

**Outputs** (usable by downstream jobs):

| Output | Description |
|---|---|
| `release_created` | `true` if a new release was created |
| `tag_name` | The tag (e.g., `v1.2.3`) |
| `major`, `minor`, `patch` | Version components |

**Example — deploy after release:**

```yaml
jobs:
  release:
    uses: NerionSoft/.github/.github/workflows/release.yml@main
    permissions:
      contents: write
      pull-requests: write

  deploy:
    needs: release
    if: ${{ needs.release.outputs.release_created == 'true' }}
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying ${{ needs.release.outputs.tag_name }}"
```

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

In template YAML files, use `$default-branch` instead of hardcoding `main`. GitHub replaces it with the actual default branch when the developer adopts the template.

```yaml
on:
  push:
    branches: [$default-branch]
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
