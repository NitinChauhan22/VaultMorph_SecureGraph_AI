# Changelog

All notable public releases of SecureGraph AI Desktop Community Edition. Dates are release
dates; internal development history is not published here.

## [Unreleased]

Initial public release preparation — no binaries published to this repository's Releases
page yet.

### Included at first public release
- SAST (Semgrep + OpenGrep) and cross-function/cross-file Taint Analysis
- SCA (OSV-Scanner), Secrets Scanning (Gitleaks, with optional live validation),
  IaC Security (Checkov), Container Security (Trivy), DAST (OWASP ZAP)
- API Threat Model, AI & MCP Security, Cryptographic Inventory (CBOM)
- Security Graph, Correlation, and Attack Paths
- Ten report types across six export formats (HTML, JSON, CSV, SARIF, CycloneDX, SPDX)
- Free, optional device registration (no trial expiry, no tiers, no scan/report/export gating)
- Local-only AI mode by default, with an AI Context Firewall and AI Audit Log when an
  external AI provider is enabled
