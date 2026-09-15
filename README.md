# SecureGraph AI

**An AI-native, local-first application security platform for developers and AppSec teams.**

SecureGraph AI runs a set of security engines against your codebase and infrastructure —
static analysis (SAST), dependency scanning (SCA), secrets detection, infrastructure-as-code
(IaC) security, container security, dynamic testing (DAST), API threat modeling, and a
cryptographic inventory (CBOM) — and correlates every finding into one **Security Graph**, so
you can see which issues are actually reachable and exploitable instead of triaging a long,
undifferentiated alert list.

This repository is the **public home** of SecureGraph AI: releases, documentation, and issue
tracking. SecureGraph AI is closed-source software; this repository does not contain its
source code.

## What it does

- **SAST + Taint Analysis** — rule-based static analysis plus cross-function/cross-file
  data-flow tracing from untrusted input to a dangerous sink.
- **SCA (Software Composition Analysis)** — known-vulnerable open-source dependencies.
- **Secrets Scanning** — hardcoded credentials and tokens, with optional live validation.
- **IaC Security** — Terraform/CloudFormation/Kubernetes misconfigurations.
- **Container Security** — base-image vulnerabilities and registry drift.
- **DAST** — consent-gated, runtime black-box testing.
- **Threat Model** — API surface mapping and threat modeling.
- **AI & MCP Security** — security posture of AI/LLM integrations and MCP servers.
- **CBOM** — a full cryptographic inventory (algorithms, key sizes, weak/deprecated flags).
- **Security Graph, Correlation & Attack Paths** — links findings across engines onto your
  real application structure and chains them into candidate exploit paths.
- **Ten report types** (Executive, Application, Security, Scan, Developer, Vulnerability,
  SBOM, CBOM, Audit, AI Governance) exported as HTML, JSON, CSV, SARIF, CycloneDX, or SPDX.

## Privacy by default

The Desktop Community edition collects zero personal data during normal use. Scanning, the
Security Graph, findings, and reports are generated and stored entirely on your device — no
analytics, no telemetry, no account required. Registration (free, optional) only unlocks a
small set of governance-dashboard extras and never gates scanning, reports, or exports. See
[docs/PRIVACY_POLICY.md](docs/PRIVACY_POLICY.md) for the full policy.

External AI providers are off by default (Local-only mode); when enabled, the AI Context
Firewall controls what — if anything — is sent, and every interaction is logged in the AI
Audit Log.

## Getting started

1. Download the latest installer from the [Releases](../../releases) page.
2. Install the scanner engine dependencies you plan to use (Semgrep, OSV-Scanner, Gitleaks,
   Checkov, Trivy, OpenGrep, OWASP ZAP) — see [docs/ADMIN_GUIDE.md](docs/ADMIN_GUIDE.md) §1–2
   for exact install commands per engine.
3. Open SecureGraph AI, add your project, and run your first scan.

Full documentation:

- [User Guide](docs/USER_GUIDE.md) — concepts, engines, running scans, reading reports.
- [Admin Guide](docs/ADMIN_GUIDE.md) — installation, licensing, engine setup, troubleshooting,
  AI governance, SIEM/enterprise deployment.
- [Privacy Policy](docs/PRIVACY_POLICY.md)

Documentation and downloads are also published at
[vaultmorph.org/securegraph](https://vaultmorph.org/securegraph).

## Support

- Bugs and feature requests: open an [issue](../../issues) in this repository.
- Privacy requests: contact@vaultmorph.org
- Product homepage: [vaultmorph.org](https://vaultmorph.org)

## License

SecureGraph AI is proprietary software, free to use in its Desktop Community edition. See
[LICENSE](LICENSE) for the full terms. Third-party components used by SecureGraph AI are
listed in [NOTICES.md](NOTICES.md).
