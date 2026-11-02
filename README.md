# NerionSoft GitHub Standards

This repository holds the organization's community health files and its **workflow templates**. The reusable CI/CD workflows themselves live in [NerionSoft/nerionsoft-cicd](https://github.com/NerionSoft/nerionsoft-cicd), whose README documents every input, output and secret.

## Repository structure

```text
workflow-templates/                       # Starter workflows (Actions > New workflow > "By NerionSoft")
  nerionsoft-lint-ci.yml                  # → nerionsoft-cicd reusable-lint-ci.yml
  nerionsoft-nextjs-ci.yml                # → nerionsoft-cicd reusable-nextjs-ci.yml
  nerionsoft-python-ci.yml                # → nerionsoft-cicd reusable-python-ci.yml
  nerionsoft-release.yml                  # → nerionsoft-cicd reusable-release.yml
  <name>.properties.json                  # Template metadata
profile/                                  # Organization profile page
CONTRIBUTING.md, SECURITY.md, ...         # Community health files (defaults for every repo)
```

## How the templates work

Each template is a small file, copied into the repository that adopts it, that calls a reusable workflow of nerionsoft-cicd at `@main`. The logic stays in nerionsoft-cicd: a fix there reaches every repository without touching them. Defaults are listed as comments in each template; uncomment only what differs.

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
