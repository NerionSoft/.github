# NerionSoft Contributing

Thanks for contributing to NerionSoft ❤️. This document describes how to propose changes, the expected workflow, and the quality and security requirements.

## Code of Conduct

By participating, you agree to follow our Code of Conduct: see `CODE_OF_CONDUCT.md`.

## Security Issues

Do not report security vulnerabilities via public issues, discussions, or pull requests.  
Email: security@nerionsoft.com.

## Ways to Contribute

You can:
- report a bug
- propose a feature or improvement
- improve documentation
- reduce technical debt
- improve CI/CD and tooling

Please use the issue templates in `.github/ISSUE_TEMPLATE/` when available.

## Workflow (Branches and Pull Requests)

Recommended branch model (if applicable to the repository):
- `main`: stable / production
- `integration`: staging / pre-production
- working branches: `feat/...`, `fix/...`, `chore/...`, `docs/...`, `refactor/...`, `hotfix/...`

Rules:
- Open pull requests to `integration` for standard changes.
- Critical hotfixes may target `main` and must be propagated back to `integration`.
- One pull request should have one clear goal. Avoid mixed or unrelated changes.

## Commit Messages

Follow Conventional Commits where possible:
- `feat: ...`, `fix: ...`, `docs: ...`, `refactor: ...`, `chore: ...`, `test: ...`, `ci: ...`

Keep the subject short and descriptive. Reference issues when relevant (e.g., `#123`).

## Quality Requirements

Before opening a pull request:
- run the test suite (if present)
- ensure linting/formatting passes (if configured)
- update documentation when behavior changes
- keep changes minimal and focused
- avoid committing secrets, tokens, or credentials

CI must be green for a PR to be merged.

## Pull Request Guidelines

Include in the PR description:
- context and motivation
- what changed and why
- how it was tested
- screenshots or logs when relevant
- rollout/impact notes if the change affects production workflows

Be responsive to review feedback and keep discussions technical and respectful.

## License

By contributing, you agree that your contributions will be licensed under the repository’s license.
