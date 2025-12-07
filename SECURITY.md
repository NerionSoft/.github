# Security Policy

NerionSoft is committed to maintaining the highest security standards across its infrastructure, CI/CD pipelines, and internal tooling.  
This repository contains configuration, workflows and automation logic that may affect multiple projects. Security is a top priority.

---

## 🔒 Supported Versions

We operate under a continuous delivery model.  
Therefore:

- Only the **latest commit on `main`** is considered secure.
- Older versions of workflows or configurations are not maintained.
- Vulnerabilities found in feature branches should be reported privately.

---

## 🛡️ Reporting a Vulnerability

If you believe you have found a security vulnerability, please contact us **privately and responsibly**:

**Email (preferred):** security@nerionsoft.com  
**Backup:** devops@nerionsoft.com  

### Please include:
- A detailed description of the issue  
- Steps to reproduce  
- Impact assessment (if possible)  
- Screenshots or proof-of-concept (optional)

⚠️ **DO NOT** open a GitHub Issue for security reports.  
This repository may contain automated workflows affecting production systems.

We aim to:
- Acknowledge receipt within **48 hours**  
- Provide an initial assessment within **5 days**  
- Resolve critical vulnerabilities within **7 days**

---

## 🔐 Responsible Disclosure Guidelines

We kindly ask security researchers to adhere to the following principles:

- Act in good faith and avoid impacting production systems.  
- Do not access, modify, or copy data that does not belong to you.  
- Avoid scanning that may degrade service availability.  
- Allow us reasonable time to review and fix the issue before public disclosure.  

NerionSoft greatly values the work done by ethical security researchers and will credit contributors when appropriate.

---

## 🧱 Security Controls (Overview)

Our infrastructure and CI/CD rely on:
- GitHub Actions with **OIDC authentication**
- Secrets stored in **1Password** (no secrets in the repo)
- Automated dependency updates (Renovate)
- Container and dependency scanning (Trivy)
- Code quality and security gates (SonarCloud)
- Automated linting, typing and testing
- SBOM generation for sensitive workloads

For more details on our internal security architecture, please contact us at:  
📩 security@nerionsoft.com

---

## ⚠️ Out of Scope

The following are explicitly **not** considered vulnerabilities:
- Missing HTTP security headers in preview/staging environments
- Rate limits or brute-force protections on test systems
- Vulnerabilities in third-party services outside our control
- Issues requiring physical access to developer environments

---

## 🤝 Thank You

We appreciate your efforts to help us maint
