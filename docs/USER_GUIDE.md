# SecureGraph AI — User Guide

## 1. What is SecureGraph AI?

SecureGraph AI is a GUI-first desktop security platform for developers and AppSec teams. It runs
a set of security engines against your codebase and infrastructure, correlates every finding into
one security graph, and shows you which issues are actually reachable and exploitable — not just
a long, undifferentiated list of alerts. The desktop app is the primary interface; a CLI exists for
automation and CI/CD, but every capability is reachable from the GUI first.

## 2. Core concepts: Scan vs. Observation

**Scan** is an active check you run on demand (or in CI). It executes one or more engines against
your codebase or artifact and produces raw findings for that run.

*Why it's needed*: a scan is your evidence-gathering step. Without it you're guessing whether a
library, endpoint, config file, or container image has a known weakness.

**Observation** is what SecureGraph AI builds *from* your scans over time — the Security Graph and
everything layered on it (Correlation, Attack Paths, the risk score, the Living Threat Model). It
links findings across engines onto your real application structure (services, data flows, trust
boundaries) and tracks how your risk posture changes scan over scan. It answers "is this finding
connected to something that matters, and is it getting better or worse?" rather than just "was
this file flagged."

*Why it's needed*: raw scanners each work in isolation and frequently re-report the same root
cause several different ways. Observation removes duplication, ranks by real exploitability
(reachability/attack-path analysis), and gives you a durable, evolving picture instead of a
one-off snapshot that goes stale the moment you close the report.

## 3. The engines — what each one does

| Engine | What it checks | Nav location |
|---|---|---|
| SAST (Static Analysis) | Code-level flaws (injection, unsafe deserialization, hardcoded logic bugs, etc.) via rule-based pattern matching | Developer Workflow → Static Analysis |
| Taint Analysis (Advanced SAST) | Data-flow tracing from untrusted input (source) to a dangerous operation (sink) across function/file boundaries — catches issues plain pattern-matching SAST misses | Developer Workflow → Taint Analysis |
| SCA (Software Composition Analysis) | Known-vulnerable open-source dependencies, by version, against CVE data | Developer Workflow → Dependencies |
| Secrets Scanning | Hardcoded credentials, API keys, tokens committed to the repo; several providers support live validation (see §7) | Developer Workflow → Secrets Scanning |
| IaC Security | Misconfigurations in Terraform/CloudFormation/Kubernetes manifests and similar infrastructure-as-code | Developer Workflow → Infrastructure as Code |
| Container Security | Base-image vulnerabilities and registry drift for your container images | Developer Workflow → Container Security |
| DAST (Dynamic Testing) | Runtime, black-box testing against a live, consent-gated target — typically reviewer-initiated, not a developer's everyday pre-commit check | Security & Risk → Dynamic Testing |
| Threat Model (API Security) | API surface mapping and threat modeling for your services | Security & Risk → Threat Model |
| AI & MCP Security | Security posture of AI/LLM integrations and MCP (Model Context Protocol) servers your code talks to | Security & Risk → AI & MCP Security |
| CBOM (Cryptographic Inventory) | Every cryptographic asset in use (algorithms, key sizes, padding modes) and whether it's weak, deprecated, or strong | Security & Risk → Cryptographic Inventory |

Findings from every engine roll up into one cross-cutting **Findings** view, and into the
**Security Graph** for correlation.

## 4. Before your first scan: engine dependencies

Most engines in §3 drive a separate command-line tool that has to be installed on your machine
first — SecureGraph AI does not silently bundle or auto-install these (Taint Analysis is the one
exception; it's fully built in). If you run a scan before its tool is installed, that engine's
result will show an error like `"<tool> binary not usable on PATH: ..."` instead of findings — the
other engines you *do* have installed still run and report normally.

| Engine | Needs |
|---|---|
| SAST | Semgrep (`pip install semgrep`) |
| SAST (second engine) | OpenGrep (standalone binary download) |
| SCA | OSV-Scanner (standalone binary + a one-time database download) |
| Secrets Scanning | Gitleaks (standalone binary) |
| IaC Security | Checkov (`pip install checkov`) |
| Container Security | Trivy (standalone binary + a one-time database download) |
| DAST | OWASP ZAP (separate download, not auto-installed) |

Full install commands, download links, and a troubleshooting table for common setup errors are in
the **Admin Guide §2–4** — Settings → Scanner Tool Locations also shows exactly which path the app
found for each tool, and has an Auto-detect button.

## 5. Running a scan — how to

1. Open SecureGraph AI and select or add your project/repository.
2. Go to **Scan** and choose scope: full workspace, a specific path, or a specific engine subset
   (SAST only, SCA only, etc.).
3. Click **Run Scan**. Progress and per-engine status show live.
4. When complete, findings land in the **Findings** view, already deduplicated and enriched with
   CWE/CVE metadata where the underlying engine provides it.

Command-line equivalent (CI/automation): the `securegraph` CLI supports the same scope flags and
the same report fields as the GUI — see the Admin Guide (§2) for wiring it into a pipeline.

## 6. The Security Graph, Correlation & Attack Paths

Once you have scans across multiple engines, three views build on top of them:

- **Security Graph** — a navigable graph of your application's components, data flows, and
  findings, with neighbor-relationship exploration.
- **Correlation** — groups findings that share a root cause or are duplicates reported by
  different engines, so you review one grouped issue instead of five near-identical ones.
- **Attack Paths** — chains correlated findings into candidate exploit paths, so you can see which
  combinations of issues actually add up to something exploitable, not just a pile of individually
  low/medium findings.

## 7. Dashboards — which one is for whom

| Dashboard | Audience | What it shows |
|---|---|---|
| Executive | Leadership | Overall posture, trend, top risks in plain language |
| Security | Security team | Aggregate findings, coverage, and risk across the portfolio |
| Repository Risk | AppSec / engineering leads | Per-repository risk scoring across everything you scan |
| Application Dashboard | Engineering leads / developers | Deep dive into one specific application/repository |

## 8. Findings & their lifecycle

Each finding carries: severity, CWE/CVE identifiers where applicable, exact file/line location (or
an honest "not provided by `<scanner>`" label — see §9 — when the underlying tool genuinely didn't
report that field), and remediation guidance. Findings have an explicit lifecycle (open, triaged,
resolved, etc.) you can set from the Findings or Finding Detail screen, so status persists across
re-scans instead of a finding silently reappearing as "new" every run.

**Secret validation**: for several secret-scanning providers, SecureGraph AI can optionally live-
validate a discovered credential (i.e. check whether it's actually active) rather than just
pattern-matching it — configured per-provider in Settings, opt-in, and each provider discloses
exactly where the validation check is sent.

## 9. Reports — the ten report types

| Report type | What it covers | Who it's for |
|---|---|---|
| Executive | Overall security score, trend, top risks in plain language | Leadership, auditors |
| Application | Full inventory: services, components, findings by severity, remediation plan | Engineering leads, AppSec |
| Security | Aggregate cross-portfolio security posture | Security team |
| Scan | Per-engine results for one specific scan run | Developers fixing that run's issues |
| Developer | Developer-focused view of open findings for a repository | Developers |
| Vulnerability | Deep dive on a specific finding/vulnerability | Developers, AppSec |
| SBOM | Full software bill of materials (dependency inventory) | Compliance, supply-chain review |
| CBOM | Full cryptographic bill of materials, with weak/deprecated/strong classification | Compliance, crypto review |
| Audit | Audit trail of actions taken in the app | Security officers, compliance |
| AI Governance | What AI features were used, what data left the machine (if any), and under what policy | Security officers, compliance |

**Export formats**: HTML (with print-ready CSS — use your browser's Print to PDF for a PDF copy),
JSON, CSV, SARIF, CycloneDX, and SPDX (the last two for SBOM/CBOM interchange with other tools).

## 10. Reading a report — conventions to know

- **"Not provided by `<scanner>`"** — the underlying engine genuinely didn't report that field
  (e.g. no CWE mapping available). This is shown instead of guessing, and is not an error in
  SecureGraph AI.
- **"Stale"** — an open critical or high-severity finding still open more than 30 days after it
  was first detected. A Stale-tagged item is a process signal (it's been sitting too long), not a
  statement about the finding's technical severity.
- **Application security score** — computed as: start at 100, subtract 18 points per open
  critical finding, subtract 7 points per open high finding, subtract up to 20 points total for
  every other open finding combined, floored at 0.
- **Score bands**: **Strong** = 90–100, **Managed** = 75–89, **Needs attention** = 60–74,
  **High risk** = below 60.
- Every report type that can show a judgement word (Strong/Stale/etc.) defines it in its own
  **Definitions** (or Glossary/Risk criteria) section — read that once per report type rather than
  guessing what a label means.

## 11. What to expect

- First scan on a large codebase takes longer; subsequent scans are faster due to caching.
- Not every finding is exploitable — use Correlation/Attack Paths to prioritize instead of working
  top-to-bottom by severity alone.
- Your source code is not silently sent to external AI providers — see the Admin Guide's AI
  Context Firewall section, and the in-app first-run Privacy Notice.
- Registering (Settings → License → Register) is free and optional; it unlocks governance-
  dashboard extras only and does not gate scanning, reports, or exports.

## 12. Getting help

In-app: sidebar → **Help & Guides** (this content, plus the Admin Guide, fully offline).
Website: vaultmorph.org/securegraph/docs. Privacy Policy: vaultmorph.org/securegraph/privacy.
