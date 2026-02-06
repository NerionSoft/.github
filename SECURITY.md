# Security

NerionSoft takes the security of its repositories, CI/CD workflows, and automation seriously.  
Please report suspected vulnerabilities privately.

## Supported Versions

We operate under a continuous delivery model:
- Only the latest commit on `main` is supported.
- Older workflow/config revisions are not maintained.

## Reporting a Vulnerability

Do not report security issues via public GitHub issues, discussions, or pull requests.

Instead, email:
- security@nerionsoft.com (preferred)
- devops@nerionsoft.com (backup)

Please include, when possible:
- Issue type (e.g., RCE, injection, auth bypass)
- Affected files/paths and location (branch/commit)
- Steps to reproduce (minimal PoC if available)
- Impact and expected vs actual behavior
- Any relevant logs or screenshots

## Responsible Disclosure

Please:
- Act in good faith and avoid disrupting production systems
- Avoid accessing or exfiltrating data that is not yours
- Give us reasonable time to investigate and remediate before public disclosure

## Out of Scope

The following are generally out of scope:
- Issues in third-party services outside our control
- Findings requiring physical access to developer environments
- Preview/staging-only hardening gaps with no production impact
