# Changelog

All notable public releases of SecureGraph AI Desktop Community Edition. Dates are release
dates; internal development history is not published here.

## [1.0.0] - 2026-09-15

First public release.

- SAST (Semgrep + OpenGrep) and cross-function/cross-file Taint Analysis
- SCA (OSV-Scanner), Secrets Scanning (Gitleaks, with optional live validation),
  IaC Security (Checkov), Container Security (Trivy), DAST (OWASP ZAP)
- API Threat Model, AI & MCP Security, Cryptographic Inventory (CBOM)
- Security Graph, Correlation, and Attack Paths
- Ten report types across six export formats (HTML, JSON, CSV, SARIF, CycloneDX, SPDX)
- Free, optional device registration (no trial expiry, no tiers, no scan/report/export gating)
- Local-only AI mode by default, with an AI Context Firewall and AI Audit Log when an
  external AI provider is enabled

Installers are signed with VaultMorph's code-signing certificate. See
[CHECKSUMS.md](CHECKSUMS.md) for SHA256 hashes to verify your download.
